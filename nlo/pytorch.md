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



