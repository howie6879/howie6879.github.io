---
categories:
  - Machine Learning
date: 2019-03-27 08:56:00+08:00
image: /images/thumbs/h_42.jpg
tags: [神经网络与深度学习]
title: "字符级CNN分类模型的实现"
markup: mmark
toc: true
comments: true
---

上次发了一条字符级分类模型的推文[读 - Character-level Convolutional Networks for Text Classiﬁcation](https://www.howie6879.cn/post/2018/42/)

这次是对字符级CNN分类论文进行了代码实现：[1509.01626 Character-level Convolutional Networks for Text Classification](https://arxiv.org/abs/1509.01626)

项目环境：

- Python3.6
- Anaconda+Pipenv管理

### 使用

```shell
# 下载代码
git clone https://github.com/howie6879/char_cnn_text_classification.git

# 利用anaconda建立Python3.6环境 
conda create -n python36 python=3.6

# 进入项目 
cd char_cnn_text_classification

# --python 后面的路径是上面conda创建的路径地址
pipenv install --python  ~/anaconda3/envs/python36/bin/python3.6

# 如果出错 否则跳过这段
pipenv run pip install pip==18.0

# 安装依赖 具体以来可查看Pipenv文件
pipenv install
# 进入代码目录
cd char_cnn_text_classification
```

### 模型

模型结构和论文中介绍的一样：

![](https://ws1.sinaimg.cn/large/007i3XCUgy1fyx2mmtvazj30jh05xdgr.jpg)

论文中设计了`large`和`small`两种卷积网络，分别对应不同大小的数据集，且都由6个卷积层和3个全连接层共9层神经网络组成

对于英文数据，如果数据集不大，可以考虑使用包含大小写的字母表

#### 数据集

**ag_news_csv**：新闻数据

对于英文数据，包含在*[ag_news_csv]*(char_cnn_text_classification/datasets/ag_news_csv)文件夹里面，信息如下：

- 训练集：120000
- 测试集：7600
- 类别：4

数据集处理类*[DataUtils]*(char_cnn_text_classification/utils/data_utils.py)，这里以训练集`shape`为例：

- Input实例：(120000, 1014) 
- Label：(120000, 4)

#### 配置

关于配置，请参考*[Config]*(char_cnn_text_classification/config/config.py)类：

```python
# 字母表
alphabet = "abcdefghijklmnopqrstuvwxyz0123456789-,;.!?:'\"/\\|_@#$%^&*~`+-=<>()[]{}"
alphabet_size = len(alphabet)
# 输入大小，即论文中的l0
input_size = 1014
# 训练集类别
num_of_classes = 4

batch_size = 128
epochs = 1000

checkpoint_every = 100
evaluate_every = 100

# 激活函数的 threshold 值
threshold = 1e-6
# 防止过拟合 dropout保留比例
dropout_p = 0.5


# 损失函数
loss = 'categorical_crossentropy'
# 优化器 rmsprop adam
optimizer = 'adam'
```

#### 训练

配置好环境之后，可以直接进行训练：

```shell
python run_model.py
```

可以在测试集分出20000条作为验证集进行训练

```shell
Data loaded from datasets/ag_news_csv/train.csv
CharCNN model built success:
......
Training Started ===>
Train on 100000 samples, validate on 20000 samples
Epoch 1/10
......
100000/100000 [==============================] - 4338s 43ms/step - loss: 0.9999 - acc: 0.5329 - val_loss: 0.6755 - val_acc: 0.7290
Epoch 2/10
......
100000/100000 [==============================] - 4265s 43ms/step - loss: 0.5044 - acc: 0.8204 - val_loss: 0.4582 - val_acc: 0.8405
Epoch 3/10
......
100000/100000 [==============================] - 4268s 43ms/step - loss: 0.3593 - acc: 0.8799 - val_loss: 0.4177 - val_acc: 0.8522
......
```

迭代了三轮，就达到了论文中所说的效果`0.8522`

准确率和误差图示：

![](https://ws1.sinaimg.cn/large/007i3XCUgy1fyx2n12h82j31e00xc0ui.jpg)
![](https://ws1.sinaimg.cn/large/007i3XCUgy1fyx2njpt2jj31e00xcdhs.jpg)

可以看到，迭代6、7轮后的结果挺不错，也可以利用`Tensorboard`进行可视化：

```shell
tensorboard --logdir=char_cnn_text_classification/logs
```

#### 测试

```python
char_cnn_model.model.evaluate(test_inputs, test_labels, batch_size=Config.batch_size, verbose=1)
```

可以得到结果输出：

```shell
128/7600  [..............................] - ETA: 1:51
......
7600/7600 [==============================] - 110s 15ms/step
[0.41680785787732977, 0.8789473684210526]
```

其中：

- loss: 0.41
- acc: 0.8789

### 说明

感谢论文作者`Xiang Zhang, Junbo Zhao, Yann LeCun`，以及下面这些开源项目：

- [GitHub - mhjabreel/CharCNN](https://github.com/mhjabreel/CharCNN)
- [GitHub - mhjabreel/CharCnn_Keras: The implementation of text classification using character level convoultion neural networks using Keras](https://github.com/mhjabreel/CharCnn_Keras)

