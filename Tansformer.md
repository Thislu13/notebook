#深度学习/NLP 
# Tansformer

> 本质是加权求和     编码和解码是基础

![image-20240713160452077](http://cdn.jsdelivr.net/gh/Thislu13/image_save@main/notebook/202407131604001.png)



* 图片里的点是并行的   语言是有先后关系的   同时也是 RNN 与 CNN 区别

* RNN解决 seq2seq 机器翻译输入输出长短不同

* 根据单词的上下文关系来找到单词的意思（计算机无法理解现实中的示例）

  > 例如香蕉的上下文 与 猴子 黄色 甜高度相关

* 将词义通过一个高纬度空间展示出来 （one-hat 与 分词器对token的两个极端 ）
  * 数字化
  * 距离有实际含义   
    * one-hat（过于稀疏） 
      * 所有token都是正交的很难标识其关系
    * 分词器（过于稠密） 
      * 在两个词关联时候容易与第三个冲突
      * 有相关的词可能实际距离较远

* 找到一个**潜空间**

* 向量与矩阵相乘一种理解方式是 空间变换（映射关系） 是线性变换（旋转、拉伸）存在一一对应关系

  * 可以认为是向量本身的改变 (维度不变的情况下)

  * 可以认为产生变换的是矩阵空间，向量在每个维度的分向量（投影）经过矩阵变换（乘以对应的值）

    再将相乘之后得到的结果相加（向量的合成）得到原来的向量

    ![image-20240713160554038](http://cdn.jsdelivr.net/gh/Thislu13/image_save@main/notebook/202407131605929.png)

  * 也可以将上面结合认为同时发生改变

![image-20240713160649383](http://cdn.jsdelivr.net/gh/Thislu13/image_save@main/notebook/202407131606238.png)


* 二次型可以认为是 平方操作

$$
P(x) = x^TAx \approx f(x) = ax^2
$$

![image-20240713160753692](http://cdn.jsdelivr.net/gh/Thislu13/image_save@main/notebook/202407131607928.png)

* 以上可知在矩阵乘法的过程中 
  * 第一个数代表 多个向量集合  
  * 第二个数代表 变换的规则(流处理，类比于函数、一维可认为是系数)   
  * 值是变换的结果
  * 因此矩阵不存在交换律
  * 因为是点与点的映射 可能会保留原来的一定的特性（比如向量与向量之间的倍率关系、平行）

* 编码（Encoding）  降维 相当于把 one-hot 降维  （嵌入矩阵）  空间是连续的，所以就算之前未出现的东西也能在其中找到对应位置（超经验）

  * Word2Vec 非常规，希望得到是变换矩阵 

    * CBOW 输入奇数个TOKEN 去掉一个 将其余偶数个投入变换矩阵 将四个新向量加在一起 得到一个新的 和向量 这个应该与被挖掉的相同 计算其损失值参考力的合成与分解，用其他词表达挖掉的词，理解为  在什么什么环境下，需要这个词，也就是词义！
    无需关心具体要填什么，目的是得到这个转化矩阵
      
      **客观的词义由主观的人组合在一起得到语义**
    得到的结果非人写文字的主观表达，是抛去人主管的客观含义（就是语义）
      
    * skip-gram 用一个 去猜其余几个

* 注意力

![image-20240712093109268](http://cdn.jsdelivr.net/gh/Thislu13/image_save@main/notebook/202407120931825.png)

![image-20250124213916605](https://raw.githubusercontent.com/Thislu13/image_save/main/notebook/202501242139738.png)

此处Mask使得当前只看$k_{t-1}$状态之前的数据，将$k_t$之后的部分置换为一个非常大的负数，经过softmax之后无限接近零，可以忽略

![image-20250124215815723](https://raw.githubusercontent.com/Thislu13/image_save/main/notebook/202501242158757.png)

> 多头注意力的原理，实际是通过多组线性层的映射（$W$）尝试将 $QKV$ 映射到不同的空间内，模仿卷积过程中产生的不同通道 

![image-20250125104201182](https://raw.githubusercontent.com/Thislu13/image_save/main/notebook/202501251042208.png)

> Feed Forward 就是三维的MLP