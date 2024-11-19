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
