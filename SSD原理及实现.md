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

