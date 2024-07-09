#包
# Numpy

## 1. ndarray

### 1.1 ndarray属性

数组更符合科学计算的思想，将注意力转移到数组本身而非单个数据的计算

底层由C实现，更高效快捷

多维数组对象由两部分组成

* __实际数据__ 

*  __描述这些数据的原数据__（数据维度、类型）

   `ndarray`

  > * 轴(axis)  保存数据的维度
  > * 秩(rank) 轴的数量
  >
  > | 基本属性  | 说明                    |
  > | --------- | ----------------------- |
  > | .ndim     | 秩 轴的数量、数据的维度 |
  > | .shape    | 尺寸 对于矩阵n行m列     |
  > | .size     | 元素个数 n*m            |
  > | .dtype    | 元素类型                |
  > | .itemsize | 单个元素的大小          |
  >
  > | 数据类型   | 说明                          |
  > | ---------- | ----------------------------- |
  > | bool       | Flase、Ture                   |
  > | intc       | int32 或 int64 与系统有关     |
  > | intp       | 索引                          |
  > | int8       |                               |
  > | int16      |                               |
  > | int32      |                               |
  > | int64      |                               |
  > | uint8      |                               |
  > | uint16     |                               |
  > | uint32     |                               |
  > | uint64     |                               |
  > | float16    |                               |
  > | float32    |                               |
  > | float64    |                               |
  > | complex64  | 复数， 实部虚部都是32 float位 |
  > | complex128 | 复数， 实部虚部都是64 float位 |

  > ndarray中可以用不同的数据类型，在存储过程中`dtype`被当作单个`object`不能发挥其优势
  >
  > 尽量不适用

### 1.2创建 ndarray

* `x = np.array(list/tuple, dtype=np.dtype)` 可指定数据类型

  > `dtype`未声明会自适应

* 使用自带函数

  | 函数                 | 说明                          |
  | -------------------- | ----------------------------- |
  | np.arange(n)         | 类似range 0 ~ n-1             |
  | np.ones(shape)       | shape 形状的 1                |
  | np.zeros(shape)      | shape 形状的 0                |
  | np.full(shape, val)  | shape 形状的 val              |
  | np.eye(n)            | n*n    对角线为1其余为0的矩阵 |
  | np.ones_like(a)      | 根据数组a ...                 |
  | np.zeros_like(a)     | 根据数组a ...                 |
  | np.full_like(a, val) | 根据数组a ...                 |

* 其他方法

  | 函数                            | 说明                       |
  | ------------------------------- | -------------------------- |
  | np.linspace(l, r, n, endpoint=) | 根据起止数据等间距生成数组 |
  | np.concatenate()                | 合并形成新数组             |

### 1.3改变数据

| 改变数据大小方法            | 说明             |
| ------------------- | -------------- |
| .reshape(shape)     | 返回新的数组，原数组大小不变 |
| .reszie(shape)      | 修改原数组          |
| .swapaxes(ax1, ax2) | 调换两个维度         |
| .flatten            | 折叠为一维数组，不改变原数组 |

| 改变数据类型方法  | 说明                     |
| ----------------- | ------------------------ |
| .astype(new_type) | 返回一个新数据类型的数组 |
| .tolist()         | 返回一个列表             |

### 1.4操作

> [2:10]  该维度取 2-9
>
> [:]  取该维度所有数据
>
> [::2] 以步长为二取

核心是希望将ndarray当成一个数进行计算