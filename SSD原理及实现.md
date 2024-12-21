## 原理

**单发多框检测**

首先通过基本网络（深度卷积网络）从输入图像中提取特征，随后逐层卷积多尺度进行锚框与分类任务

![image-20241119213426150](https://raw.githubusercontent.com/Thislu13/image_save/main/notebook/202411192134405.png)

> 因为后续要进行多尺度特征提取，因此基本网络最后的输出高宽应该保持在一个较大的尺度，方便检测较小目标

# 实现细节

## 类别预测以及边界预测

```python
def cls_predictor(num_inputs, num_anchors, num_classes):
	return nn.Conv2D(num_inputs,num_anchors*(num_classes+1),kernel_size=3,padding=1)
```

> num_anchors: 单个检测点，检框数量
>
> num_classes:  分类任务的种类
>
> 输出通道变为，num_anchors*(num_classes+1) 因为要对每一锚框内的物体进行分类，分类的种类包括所有的任务需求，加上作为背景的负样本

```python
def bbox_predictor(num_inputs, num_anchors):
	return nn.Conv2D(num_inputs, num_anchors*4, kernel_size=3, padding=1)
```

> 每个锚框包括四个特征值（例如左上、右下两点坐标）

## 引入多尺度

在不同尺度的特征图中，可以提取到不同的目标

但是不同尺度的特征图经过预测网络得到输出的尺寸存在差异（例如，在类别预测过程中，不同层需要检测的目标框类别有差异，因此输出存在差异），往往只有0维，也就是batch_size一致

为了将这多个个预测输出链接起来以提高计算效率，我们需要把这些张量转换为更一致的格式。

为了保证同一特征层的值具有连贯性，我们将第一维移动到最后，再将其拉成二维向量，随后将多个二维向量进行拼接。

```python
def flatten_pred(pred):
    return torch.flatten(pred.permute(0, 2, 3, 1), start_dim=1)

def concat_preds(preds):
    return torch.cat([flatten_pred(p) for p in preds], dim=1)
```

## 基础模块

将输入的图片进行特征提取，使用 `down_sample_blk` 将图片高宽减半（合理使用填充为1，3*3 的卷积、 2 *2的最大汇聚层）

```python
def down_sample_blk(in_channels, out_channels):
    blk = []
    for _ in range(2):
        blk.append(nn.Conv2d(in_channels, out_channels,
                             kernel_size=3, padding=1))
        blk.append(nn.BatchNorm2d(out_channels))
        blk.append(nn.ReLU())
        in_channels = out_channels
    blk.append(nn.MaxPool2d(2))
    return nn.Sequential(*blk)
```

以上采用了两个 填充1，3 * 3 的卷积 加上 2 * 2 的最大汇聚层，此时每个像素有 1*2 + （3-1）+（3-1）的感受野。

通过三个这样逐层减半的模块实现基本的特征提取

```python
def base_net():
    blk = []
    num_filters = [3, 16, 32, 64]
    for i in range(len(num_filters) - 1):
        blk.append(down_sample_blk(num_filters[i], num_filters[i+1]))
    return nn.Sequential(*blk)

forward(torch.zeros((2, 3, 256, 256)), base_net()).shape
```

## 完整网络

```python
def get_blk(i):
    if i == 0:
        blk = base_net()
    elif i == 1:
        blk = down_sample_blk(64, 128)
    elif i == 4:
        blk = nn.AdaptiveMaxPool2d((1,1))
    else:
        blk = down_sample_blk(128, 128)
    return blk
```

对于每层blk都需要提取需要的操作，包括锚框、类别、偏移量。

```python
def blk_forward(X, blk, size, ratio, cls_predictor, bbox_predictor):
    Y = blk(X)#保证模型继续进行特征提取，逐层卷积
    anchors = d2l.multibox_prior(Y, sizes=size, ratios=ratio)# 生成锚框
    cls_preds = cls_predictor(Y)# 类别
    bbox_preds = bbox_predictor(Y)# 偏移量
    return (Y, anchors, cls_preds, bbox_preds)
```

生成目标框的参数

```python
sizes = [[0.2, 0.272], [0.37, 0.447], [0.54, 0.619], [0.71, 0.79],
         [0.88, 0.961]]
ratios = [[1, 2, 0.5]] * 5
num_anchors = len(sizes[0]) + len(ratios[0]) - 1
```

完整模型

```python
class TinySSD(nn.Module):
    def __init__(self, num_classes, **kwargs):
        super(TinySSD, self).__init__(**kwargs)
        self.num_classes = num_classes
        idx_to_in_channels = [64, 128, 128, 128, 128]
        for i in range(5):
            # 即赋值语句self.blk_i=get_blk(i)
            setattr(self, f'blk_{i}', get_blk(i))
            setattr(self, f'cls_{i}', cls_predictor(idx_to_in_channels[i],
                                                    num_anchors, num_classes))
            setattr(self, f'bbox_{i}', bbox_predictor(idx_to_in_channels[i],
                                                      num_anchors))

    def forward(self, X):
        anchors, cls_preds, bbox_preds = [None] * 5, [None] * 5, [None] * 5
        for i in range(5):
            # getattr(self,'blk_%d'%i)即访问self.blk_i
            X, anchors[i], cls_preds[i], bbox_preds[i] = blk_forward(
                X, getattr(self, f'blk_{i}'), sizes[i], ratios[i],
                getattr(self, f'cls_{i}'), getattr(self, f'bbox_{i}'))
        anchors = torch.cat(anchors, dim=1)
        cls_preds = concat_preds(cls_preds)
        cls_preds = cls_preds.reshape(
            cls_preds.shape[0], -1, self.num_classes + 1)
        bbox_preds = concat_preds(bbox_preds)
        return anchors, cls_preds, bbox_preds
```



