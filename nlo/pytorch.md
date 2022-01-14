# Pytorch详细

## 一. tensor入门

### 1. 构造

```python
x = torch.empty(5,3,2) # 输入维度,构造一个未初始化的tensor
x = torch.rand(5, 3) # 输入维度,构造一个随机数tensor
x = torch.zeros(5, 3, dtype=torch.long) # 输入维度,返回全0
# 通过dtype指定类型

x = torch.tensor([5.5, 3]) # 根据内容构造tensor
```

### 2. 操作

```python
# 矩阵加法
x + y
torch.add(x,y)
x.add_(y) # 带_表示会改变x
torch.add(x,y,out=result) # 将结果存入result变量,result必须是tensor并且可能会resize
```

```python
# 重新塑形
x = torch.randn(4, 4)
y = x.view(16)
z = x.view(-1, 8)  # -1表示不确定,结果为 16 / 8 = 2
print(x.size(), y.size(), z.size())
# 获取数值
# 如果tensor只有一个数值
x.item()
```

[tensor操作大全](https://pytorch.org/docs/stable/torch.html)

## 二. autograd自动求导

### 1. tensor与autograd

如果tensor的属性`requires_grad`为true,那么tensor会追踪所有操作,完成计算后使用`backward()`来自动计算所有变量的梯度

并累加到`grad`属性

并且记录了`Function`图,在`grad_fn`属性中

### 2. 一些操作

### 2. 一些操作

`detach()`: 将tensor与计算历史分离

`with torch.nograd()`: 在此代码块中不进行梯度运算

`backward()`: 如果tensor是标量则不需要参数,如果不是就应该指定形状匹配的tensor

`x.requires_grad_(T/F)`改变x的grad属性

### 3. 实例

```python
x = torch.ones(2, 2, requires_grad=True)
y = x + 2
z = y * y * 3
out = z.mean()

out.backward(); # 开始反向传播到x

print(x.grad) # 记录了对于out的梯度
```



## 三. 神经网络

`torch.nn`

### 1. Module

`nn.Module`包含各个层和一个forward(input) 返回一个output

```python
import torch
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):

    def __init__(self):
        super(Net, self).__init__()
        # 1 input image channel, 6 output channels, 5x5 square convolution
        # kernel
        # 卷积,暂时看不懂
        self.conv1 = nn.Conv2d(1, 6, 5)
        self.conv2 = nn.Conv2d(6, 16, 5)
        # an affine operation: y = Wx + b
        # 定义各个线性参数的大小(左,右)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        # Max pooling over a (2, 2) window
        x = F.max_pool2d(F.relu(self.conv1(x)), (2, 2))
        # If the size is a square you can only specify a single number
        x = F.max_pool2d(F.relu(self.conv2(x)), 2)
        x = x.view(-1, self.num_flat_features(x))
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

    def num_flat_features(self, x):
        size = x.size()[1:]  # all dimensions except the batch dimension
        num_features = 1
        for s in size:
            num_features *= s
        return num_features


net = Net()
print(net)
```

### 2. 使用net

`net.parameters()`返回网络的参数,这个参数是什么我暂且蒙在鼓里

```python
input = torch.randn(1, 1, 32, 32)
out = net(input)
print(out)
```

`net.zero_grad()`清空梯度计数器