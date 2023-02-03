---
title: Mastering Pandas 01
date: 2016-11-21T14:23:27
categories:
  - Python
tags: []
---

## 1.pandas特性

对于python开发者来说，在面对海量数据时，`pandas`可谓是数据分析的首选，以下关键特性是它如此热门的原因：
    1. 可以处理各种不同格式的数据集：时间序列，表格，矩阵数据
    2. 促进csv、DB/SQL等来源数据的加载/导入
    3. 可以在很大数据集的基础上进行一些过滤、合并、切片等一系列操作
    4. 可以根据使用的自定义的规则来处理缺失数据
    ...
更多信息可移步[pandas](https://github.com/pandas-dev/pandas)进一步了解。
### 1.1.安装
``` python
# 可使用 anaconda 集成环境 自带科学计算包
pip install pandas
```
### 1.2.说明
建议使用`ipython`，本人使用`jupyter notebook`

## 2.pandas 数据结构
看完本篇文章后，您将了解以下数据结构：
    1. pandas是基于NumPy构建的，所以numpy.ndarray数据结构必不可少
    2. pandas.Series：一维数据结构
    3. pandas.DataFrame：二维数据结构
    4. pandas.Panel：三维数据结构
<!-- more -->

### 2.1.NumPy ndarray

在数值计算中，`NumPy`非常重要，其中最重要的数据结构便是ndarray多维数组对象，`NumPy`提供了许多方法来创造数组。

首先导入numpy模块


```python
import numpy as np
```

#### 2.1.1.numpy.array


```python
data_01 = [0, 1, 2, 3]
data_02 = [-1, -2, 5.5, 6]
# 一维数组
arr_01 = np.array(data_01)
arr_01
```


    out:
    array([0, 1, 2, 3])
```python
# 二维数组
arr_02 = np.array([data_01, data_02])
arr_02
```


    out:
    array([[ 0. ,  1. ,  2. ,  3. ],
           [-1. , -2. ,  5.5,  6. ]])


```python
# shape表示各维度大小的元组
arr_02.shape
```


    out:
    (2, 4)
```python
# dtype说明数组数据类型的对象
arr_01.dtype
```


    out:
    dtype('int64')
```python
arr_02.dtype
```


    out:
    dtype('float64')
```python
# ndim表示数组的维度
arr_01.ndim
```


    out:
    1
```python
arr_02.ndim
```


    out:
    2

#### 2.1.2.numpy.arange


```python
arr_03 = np.arange(12)
arr_03
```


    out:
    array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11])
```python
# 可以设定条件 开始数值 结束数值 间隔数
arr_04 = np.arange(3, 10, 3)
arr_04
```


    out:
    array([3, 6, 9])

#### 2.1.3.numpy.linspace
在设定的开始结束数值之间生成线性均匀间隔的元素


```python
arr_05 = np.linspace(0, 2.0/3, 4)
arr_05
```


    out:
    array([ 0.        ,  0.22222222,  0.44444444,  0.66666667])

#### 2.1.4.numpy.ones


```python
# 看代码就造用法了
arr_06 = np.ones((3, 3), dtype=np.int)
arr_06
```


    out:
    array([[1, 1, 1],
           [1, 1, 1],
           [1, 1, 1]])

#### 2.1.5.numpy.zeros


```python
arr_07 = np.zeros((3, 3), dtype=np.int)
arr_07
```


    out:
    array([[0, 0, 0],
           [0, 0, 0],
           [0, 0, 0]])

#### 2.1.6.numpy.eye


```python
arr_08 = np.eye(4, dtype=int)
arr_08
```


    out:
    array([[1, 0, 0, 0],
           [0, 1, 0, 0],
           [0, 0, 1, 0],
           [0, 0, 0, 1]])

#### 2.1.7.numpy.diag


```python
arr_09 = np.diag((1, 2, 3, 4))
arr_09
```


    out:
    array([[1, 0, 0, 0],
           [0, 2, 0, 0],
           [0, 0, 3, 0],
           [0, 0, 0, 4]])


```python
arr_10 = np.diag(arr_09, k=1)
arr_10
```


    out:
    array([0, 0, 0])



#### 2.1.8.numpy.random.randn


```python
# 从标准正态分布中返回一个或多个样本值
np.random.seed(100)
arr_11 = np.random.rand(4)
arr_11
```


    out:
    array([ 0.54340494,  0.27836939,  0.42451759,  0.84477613])

#### 2.1.9.numpy.empty


```python
arr_12 = np.empty((3, 2))
arr_12
```


    out:
    array([[ 0.,  0.],
           [ 0.,  0.],
           [ 0.,  0.]])

#### 2.1.10.numpy.tile


```python
arr_02
```


    out:
    array([[ 0. ,  1. ,  2. ,  3. ],
           [-1. , -2. ,  5.5,  6. ]])


```python
arr_13 = np.tile(arr_02, 2)
arr_13
```


    out:
    array([[ 0. ,  1. ,  2. ,  3. ,  0. ,  1. ,  2. ,  3. ],
           [-1. , -2. ,  5.5,  6. , -1. , -2. ,  5.5,  6. ]])

#### 2.1.11.NumPy datatypes


```python
# int
arr_01
```


    out:
    array([0, 1, 2, 3])
```python
# 自定义类型
arr_14 = np.arange(10, dtype='float')
arr_14
```


    out:
    array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.,  9.])
```python
out:
arr_15 = np.array(['hello','NumPy']);
arr_15.dtype
```


    out:
    dtype('<U5')
```python
arr_16 = np.array([True, False])
arr_16.dtype
```


    out:
    dtype('bool')
```python
# 修改类型
arr_17 = arr_16.astype(int)
arr_17
```


    out:
    array([1, 0])

#### 2.1.12.Numpy indexing and slicing


```python
arr_01
```


    out:
    array([0, 1, 2, 3])
```python
tuple([arr_01[0],arr_01[1],arr_01[2],arr_01[-1]])
```


    out:
    (0, 1, 2, 3)
```python
arr_02
```


    out:
    array([[ 0. ,  1. ,  2. ,  3. ],
           [-1. , -2. ,  5.5,  6. ]])


```python
arr_02[1,1]
```


    out:
    -2.0
```python
# 翻转第二行数组元素
arr_02[1,::-1]
```


    out:
    array([ 6. ,  5.5, -2. , -1. ])
```python
arr_02[1,0:3:2]
```


    out:
    array([-1. ,  5.5])

#### 2.1.13.Array masking


```python
# 当需要对数组进行选择或者过滤的时候，就可使用这个功能
# 随机生成一组数据
arr_18 = np.random.randint(0,20,10)
arr_18
```


    out:
    array([ 2,  2,  2, 14,  2, 17, 16, 15,  4, 11])
```python
# 获取其中的偶数 首先将arr_18的数据掩饰成bool类型数据
evenMask = (arr_18 % 2 == 0)
# 只要是偶数 便返回True
evenMask
```


    out:
    array([ True,  True,  True,  True,  True, False,  True, False,  True, False], dtype=bool)
```python
# 输出偶数结果
arr_19 = arr_18[evenMask]
arr_19
```


    out:
    array([ 2,  2,  2, 14,  2, 16,  4])

#### 2.1.14 Copies and views
当我们对原始数据进行切片操作，此时产生的视图并没有占有新的内存。


```python
arr_01
```


    out:
    array([0, 1, 2, 3])
```python
arr_20 = arr_01[::2]
arr_20
```


    out:
    array([0, 2])
```python
# 改变arr_20的值
arr_20[0] = 4
arr_20
```


    out:
    array([4, 2])
```python
# arr_01的值同时发生改变
arr_01
```


    out:
    array([4, 1, 2, 3])
```python
# 判断两个数组是不是处于同一内存块，可用np.may_share_memory方法
np.may_share_memory(arr_01, arr_20)
```


    out:
    True
```python
np.may_share_memory(arr_18, arr_19)
```


    out:
    False
```python
# np.copy
arr_21 = arr_01.copy()
arr_21
```


    out:
    array([4, 1, 2, 3])
```python
# 改变arr_21的值
arr_21[0] = 0
arr_21
```


    out:
    array([0, 1, 2, 3])
```python
# arr_01的值没有发生改变
arr_01
```


    out:
    array([4, 1, 2, 3])
```python
# 因为此时他们分别处于不同的内存块
np.may_share_memory(arr_01, arr_21)
```


    out:
    False

#### 2.1.15.Operations


```python
# 这里随便列出一些操作
arr_02
```


    out:
    array([[ 0. ,  1. ,  2. ,  3. ],
           [-1. , -2. ,  5.5,  6. ]])


```python
arr_02.T
```


    out:
    array([[ 0. , -1. ],
           [ 1. , -2. ],
           [ 2. ,  5.5],
           [ 3. ,  6. ]])


```python
np.transpose(arr_02)
```


    out:
    array([[ 0. , -1. ],
           [ 1. , -2. ],
           [ 2. ,  5.5],
           [ 3. ,  6. ]])


```python
arr_01
```


    out:
    array([4, 1, 2, 3])
```python
arr_01.prod()
```


    out:
    24
```python
np.prod(arr_02, axis=0)
```


    out:
    array([ -0.,  -2.,  11.,  18.])
```python
np.all(arr_01 < 5)
```


    out:
    True
```python
np.any(arr_01%2 == 0)
```


    out:
    True

#### 2.1.16.Broadcasting
广播指的是对不同形状的数组进行组合（或者说算术运算）


```python
arr_01
```


    out:
    array([4, 1, 2, 3])
```python
arr_22 = np.ones([3,4])
arr_22
```


    out:
    array([[ 1.,  1.,  1.,  1.],
           [ 1.,  1.,  1.,  1.],
           [ 1.,  1.,  1.,  1.]])


```python
arr_01 + arr_22
```


    out:
    array([[ 5.,  2.,  3.,  4.],
           [ 5.,  2.,  3.,  4.],
           [ 5.,  2.,  3.,  4.]])


```python
arr_23 = np.array([[1,2,3,4]])
arr_23
```


    out:
    array([[1, 2, 3, 4]])
```python
arr_23.T + arr_23
```


    out:
    array([[2, 3, 4, 5],
           [3, 4, 5, 6],
           [4, 5, 6, 7],
           [5, 6, 7, 8]])

#### 2.1.17.Array shape manipulation


```python
arr_02
```


    out:
    array([[ 0. ,  1. ,  2. ,  3. ],
           [-1. , -2. ,  5.5,  6. ]])


```python
arr_02.ravel()
```


    out:
    array([ 0. ,  1. ,  2. ,  3. , -1. , -2. ,  5.5,  6. ])
```python
arr_02.flatten()
```


    out:
    array([ 0. ,  1. ,  2. ,  3. , -1. , -2. ,  5.5,  6. ])
```python
arr_02.flatten('F')
```


    out:
    array([ 0. , -1. ,  1. , -2. ,  2. ,  5.5,  3. ,  6. ])
```python
arr_01.reshape(2, 2)
```


    out:
    array([[4, 1],
           [2, 3]])


```python
arr_24 = np.arange(5)
arr_24.resize((8,))
arr_24
```


    out:
    array([0, 1, 2, 3, 4, 0, 0, 0])
```python
# 增加维度
arr_25 = arr_24[:, np.newaxis]
arr_25
```


    out:
    array([[0],
           [1],
           [2],
           [3],
           [4],
           [0],
           [0],
           [0]])

#### 2.1.18.Array sorting


```python
arr_01 = arr_01.reshape(2,2,)
arr_01
```


    out:
    array([[4, 1],
           [2, 3]])


```python
# 排序
arr_01.sort(axis=0)
arr_01
```


    out:
    array([[2, 1],
           [4, 3]])

NumPy大概说到这里，具体请看[NumPy](https://docs.scipy.org/doc/)进一步了解。
### 2.2.Series
Series是一种类似于一维数组的对象，它由一组数据（NumPy数据类型）以及一组相关的标签（索引）组成。


```python
# 导入包
import pandas as pd
# 创建Series数据类型
# ser=pd.Series(data, index=idx)
```

data可使用以下的任意一种：
    1. ndarry
    2. dict
    3. scalar values
#### 2.2.1.ndarry


```python
ser_01 = pd.Series(np.random.rand(7), index=range(7))
ser_01
```


    out:
    0    0.209202
    1    0.185328
    2    0.108377
    3    0.219697
    4    0.978624
    5    0.811683
    6    0.171941
    dtype: float64

#### 2.2.2.dict


```python
user = {'name':'howie', 'age':'21'}
ser_02 = pd.Series(user)
ser_02
```


    out:
    age        21
    name    howie
    dtype: object


```python
# 指定name index
ser_03 = pd.Series(user, index=['name', 'age', 'sex'], name='user')
# 不存在的index会返回NaN
ser_03
```


    out:
    name    howie
    age        21
    sex       NaN
    Name: user, dtype: object

#### 2.2.2.scalar values
这里需要注意的是index必须提供


```python
ser_04 = pd.Series('hello', index=range(3))
ser_04
```


    out:
    0    hello
    1    hello
    2    hello
    dtype: object

关于对Series的操作，其实和上述的numpy arrays类似，不过当对其进行切片时，这同样会影响到index


```python
# 查看某个索引对应的值
ser_02['name']
```


    out:
    'howie'
```python
# 修改
ser_02['sex'] = 'man'
ser_02
```


    out:
    age        21
    name    howie
    sex       man
    dtype: object


```python
# 切片
ser_01[:4]
```


    out:
    0    0.209202
    1    0.185328
    2    0.108377
    3    0.219697
    dtype: float64


```python
ser_01[ser_01 > 0.5]
```


    out:
    4    0.978624
    5    0.811683
    dtype: float64

### 2.3.DataFrame
DataFrame是一个表格型的数据结构，可以理解为一个二维的有标签的数组。

下面介绍创建DataFrame的不同方式
#### 2.3.1.Using dictionaries of Series


```python
df_data_01 = {
    'id': pd.Series([1, 2, 3, 4, 5]),
    'name': pd.Series(['user_01', 'user_02', 'user_03', 'user_04', 'user_05']),
    'age': pd.Series(np.random.randint(20,50,5))
}
df_01 = pd.DataFrame(df_data_01, columns=['id', 'name', 'age'])
df_01
```

![df_01](http://oe7yjec8x.bkt.clouddn.com/howie/2016-11-21-062938.jpg-blog.howie)

```python
df_01.columns
```


    Index(['id', 'name', 'age'], dtype='object')
```python
df_01.index
```


    RangeIndex(start=0, stop=5, step=1)

#### 2.3.2.Using a dictionary of ndarrays/lists


```python
df_data_02 = {
    'id': [1, 2, 3, 4, 5],
    'name': ['user_01', 'user_02', 'user_03', 'user_04', 'user_05'],
    'age': np.random.randint(20,50,5)
}
df_02 = pd.DataFrame(df_data_02, columns=['id', 'name', 'age'])
df_02
```

![df_02](http://oe7yjec8x.bkt.clouddn.com/howie/2016-11-21-063030.jpg-blog.howie)

#### 2.3.3.Using a structured array


```python
df_data_03 = np.zeros((4,),dtype=[
        ('id', 'i1'),
        ('name', 'a10'),
        ('age', 'i4')
    ])
df_data_03[:] = [
    (1,'user_01',10),
    (2,'user_02',11),
    (3,'user_03',12),
    (4,'user_04',13)
]
df_03 = pd.DataFrame(df_data_03)
df_03
```

![df_03](http://oe7yjec8x.bkt.clouddn.com/howie/2016-11-21-063109.jpg-blog.howie)

#### 2.3.4.Using a Series structure


```python
ser_02
```


    out:
    age        21
    name    howie
    sex       man
    dtype: object


```python
df_04 = pd.DataFrame(ser_02,columns=['user'])
df_04
```

![df_04](http://oe7yjec8x.bkt.clouddn.com/howie/2016-11-21-063153.jpg-blog.howie)

除了以上方式生成DataFrame，还有一些其他方式，比如直接从csv、excel等直接提取生成。


```python
# 一些基本操作
df_04['user']
```


    age        21
    name    howie
    sex       man
    Name: user, dtype: object


```python
df_04['user']['age'] = 22
df_04
```

![df_04_01](http://oe7yjec8x.bkt.clouddn.com/howie/2016-11-21-063243.jpg-blog.howie)

```python
df_01.values
```


    out:
    array([[1, 'user_01', 41],
           [2, 'user_02', 39],
           [3, 'user_03', 24],
           [4, 'user_04', 24],
           [5, 'user_05', 47]], dtype=object)


```python
del df_04['user']
df_04
```

![df_04_02](http://oe7yjec8x.bkt.clouddn.com/howie/2016-11-21-063351.jpg-blog.howie)

```python
# 插入一列
df_04.insert(0,'user',[21,'user_01','man'])
df_04
```

![df_04_03](http://oe7yjec8x.bkt.clouddn.com/howie/2016-11-21-063352.jpg-blog.howie)

### 2.4.Panel
Panel是一个三维数组，它不像Series或者DataFrame那样被广泛使用，想象一下三维的三个轴，在Panel中分别如下：
    1. items axis 0
    2. major_axis axis 1
    3. minor_axis axis 2
下面介绍Panel的创建方式：
#### 2.4.1.Using 3D NumPy array with axis labels


```python
# 创建一个三维数组
panel_data_01 = np.random.randn(2, 3, 4)
panel_data_01
```


    out:
    array([[[ 0.23784462,  0.01354855, -1.6355294 , -1.04420988],
            [ 0.61303888,  0.73620521,  1.02692144, -1.43219061],
            [-1.8411883 ,  0.36609323, -0.33177714, -0.68921798]],
    
           [[ 2.03460756, -0.55071441,  0.75045333, -1.30699234],
            [ 0.58057334, -1.10452309,  0.69012147,  0.68689007],
            [-1.56668753,  0.90497412,  0.7788224 ,  0.42823287]]])


```python
panel_01 = pd.Panel(panel_data_01, 
                    items=['Item1', 'Item2'],
                    major_axis=pd.date_range('19/11/2016', periods=3),
                    minor_axis=['A', 'B', 'C', 'D']
                   )
panel_01
```


    out:
    <class 'pandas.core.panel.Panel'>
    Dimensions: 2 (items) x 3 (major_axis) x 4 (minor_axis)
    Items axis: Item1 to Item2
    Major_axis axis: 2016-11-19 00:00:00 to 2016-11-21 00:00:00
    Minor_axis axis: A to D



## 3.说明

>  笔记参考：
>  《Mastering Pandas》
>  《利用python进行数据分析》

本次笔记主要记录`pandas`数据结构，接下来将详细介绍用法。
如果您也使用`jupyter notebook`，可以下载本文源文件，点击[这里](https://github.com/howie6879/Mastering-Python/blob/master/Mastering%20Pandas/Mastering%20Pandas%2001.ipynb)