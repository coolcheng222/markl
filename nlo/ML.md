# 机器学习

## 一. 介绍

机器学习就是让机器寻找一个函数的过程.

### 1. 分类

`Regression回归`: 寻找输出一个**数值**的函数

`Classification分类`: 寻找输出一个**类别**的函数

`Structed learning`: 生成有结构性物体的函数

### 2. 训练步骤和线性模型

1. 第一步: 定义model

`Model`: 生成的带有**未知参数的function**

以最简单的**y=b+wx**线性模型为例

* `feature`: 已知参数

* `weight`: 权重,未知参数

* `bias`: 偏移,未知参数

2. 第二步: 从数据中定义一个loss

`Loss`: 一个**函数**,输入weight和bias,**衡量该组数值的优秀程度**

**L(b,w)**

* `Label`: 真实值

* `mean absolute error(MAE平均误差)`: 利用 <u>和真实值的差值 的 绝对值</u> 之 <u>平均值</u>来衡量好坏

* `Error Surface`: 根据不同参数-Loss绘制的等高线图

3. 第三步: 优化 optimization

抽取Loss最好的参数

方法: `Gradient Descent梯度下降`

思路:

* 先找一个随机的初始w值w0,计算偏微分L/w;

* 根据斜率判断应该提高w还是降低,偏移程度与`learning rate`和偏微分的大小(乘积)决定

> `hyperparameter`: 在机器学习过程中人为定义的参数,比如learning rate
>
> `local minima`: Loss局部极小值
>
> `global minima`: Loss全局最小值

### 3. 模型选择(逻辑回归)

来自model的精度限制称为`bias`

* **Piecewise Linear模型**

  * 将所有连续的曲线都近似成折线,每段折线使用 constant + a set of<img src="../pics/ML/image-20211229200803382.png" alt="image-20211229200803382" style="zoom:33%;" />(hard sigmoid)即可

  * 该形状用*Sigmoid*函数逼近,其中c为上限

    记为
    $$
    c*sigmoid(b+wx_1)
    $$
    

    ![image-20211229200955348](../pics/ML/image-20211229200955348.png)

    > sigmoid中,c改变高度,w改变斜率,b改变左右

    $$
    模型公式Activation\quad function\\
    y = b + \Sigma_ic_isigmoid(b_i+\Sigma_jw_{ij}x_1)\\
    y=b+ \Sigma_{2i}c_imax(0,b_i+\Sigma_jw_{ij}x_1)
    $$

  * 我们可以将所有未知参数,w,c,b,b集合为$\theta$向量

    > 另一种拟合线称为`ReLU`,$cmax(0,b+wx)$,两个ReLU可以形成一个hard sigmoid

2. Loss设计

   Loss进化为L($\theta$ ),依然可以使用Gradient Descent,MAE的方式,

   其中,我们称各个参数偏导的向量为`gradient`
   $$
   g=\nabla L(\theta^0)
   $$

3. loss实际操作

   将所有成果分为一个一个的Batch,每个batch生成一个结果

   然后根据batch的结果一次又一次的update theta,这整个过程称为一个`ehoch`

4. `overfitting过拟合`: 在训练过的资料上表现良好,预测时表现更差

### 附. 示例

* 不止可以做一次,可以套娃
* 每个sigmoid看做一个神经元(或hidden layer),该过程就是一个神经网络,也就是深度学习

![image-20211229202506028](../pics/ML/image-20211229202506028.png)

## 工具. pytorch

Pytorch是facebook ai机器学习框架,拥有两个特性:

* `tensor`计算(类似numpy),GPU加速
* `tape-based autograd系统`深度神经网络

![image-20211230104038702](../pics/ML/image-20211230104038702.png)

### 1. tensor

高纬度矩阵,一般存浮点数和整数(torch.float和torch.long 对应 torch.FloatTensor,torch.LongTensor)

* 构造器

  ```python
  x = torch.tensor([[1,2],[3,4]]);
  x = torch.form_numpy(np.array([[2,2],[2,2]]));
  
  x = zeros([2,3]);#0矩阵,参数为形状
  x = ones([1,2,3]);#1矩阵,参数为形状
  ```

* 运算

  ```python
  x.shape # torch.Size([2, 2]) 返回形状
  #下面看不懂
  x.squeeze(N)#去掉第n维
  x.unsqueeze(N)#增加维度
  
  x.transpose();#转置
  w = torch.cat([a,b,c],dim=1); # 根据dimension拼接矩阵
  
  x.dtype # 数据类型
  ```

  torch.cat():

  ![image-20211230110048863](../pics/ML/image-20211230110048863.png)

* 在gpu跑

  ```python
  torch.cuda.is_available() # 检查gpu是否可用
  x.to('gpu')
  ```

  

### 2. 一些使用

1. 算偏导

   实例:![image-20211230110605347](../pics/ML/image-20211230110605347.png) 

```python
x = torch.tensor([[1.,0.],[-1.,1.]],requires_grad=True)
z = x.pow(2).sum()
z.backward() # 算微分
x.grad
```

2. 读data之定义dataset

   ```python
   from torch.utils.data import Dataset,DataLoader
   class MyDataset(Dataset): #继承Dataset
       def __init__(self,file):
         self.data = ... # 读,预处理数据
       def __getitem__(self,index): #取得一个数据
         return self.data[index] 
       def __len__(self):
         return len(self.data) #返回长度
   ```

   使用dataset

   ```python
   dataset = MyDataset(file)
   dataloader = DataLoader(dataset,batch_size,shuffle=True)
   # shuffle: 是否乱序
   ```

   使用dataloader:

   每次从dataloader中可以抽取出一个batch_size大小的数据

   

### 3. 使用: 建立neural network

```python
torch.nn.Linear(输入的fearure的维度,输出的feature的维度)
```

![image-20211230111502880](../pics/ML/image-20211230111502880.png)

![image-20211230111513186](../pics/ML/image-20211230111513186.png)

![image-20211230111530613](../pics/ML/image-20211230111530613.png)

```python
#Activation Function
nn.Sigmoid()
nn.ReLU()
#Loss
nn.MSELoss()
nn.CorssEntropyLoss()
```

**建立模型**

```python
import torch.nn as nn
class MyModel(nn.Module): # 继承Module
    def __init__(self):
      super(MyModel,self).__init__()
      self.net = nn.Sequential( # 定义网
          nn.Linear(10,32),
          nn.Sigmoid(),
          nn.Linear(32,1)
      ) # 现在还感性认识,之后深入
    def forward(self,x): # 算output
      return self.net(x) # 可见net是一个函数
```

![image-20211230112829165](../pics/ML/image-20211230112829165.png)

optim:

```python
torch.opsim.SGD(params,lr,momentum=0) # 梯度下降
# 传入model.parametes,learning rate
```

### 4. 完整模板

```python
dataset = MyDataset(file) # 读数据集
tr_set = DataLoader(dataset,16,shuffle=True) # 放进loader
model = MyModel().to(device) # 设置模型在哪跑
criterion = nn.MSELoss() # 定义loss
optimizer = torch.optim.SGD(model.parameters(),0.1) # 定义优化器
```

训练过程

```python
for epoch in range(n_epochs): #自己定义跑多少epoch
    model.train()
    for x,y in tr_set:
        optimizer.zero_grad() # 把gradient重置
        x,y = x.to(device),y.to(device) #移动数据到设备(gpu)
        pred = model(x) # 计算model的output
        loss = criterion(pred,y) #算loss
        loss.backword() # 算gradient
        optimizer.step() #更新参数
```

校验过程

```python
model.eval() #设置model为evaluation mode
total_loss = 0;
for x,y in dv_set:
    x,y = x.to(device),y.to(device)
    with torch.no_grad(): # 不希望在输入时计算,提高效率
        pred = model(x)
        loss = criterion(pred,y)
    total_loss += loss.cpu().item() * len(x) # 计算loss总和
    avg_loss = total_loss / len(dv_set.dataset) # 计算平均
```

测试过程

```python
model.eval()
preds = []
for x in tt_set:
    x = x.to(device)
    with torch.no_grad():
        pred = model(x)
        preds.append(pred.cpu())
```

保存/读取

```python
# 存
torch.save(model.state_dict(),path)

# 读
ckpt = torch.load(path)
model.load_state_dict(ckpt)
```

## 附. backpropagation反向传播算法

基于深度学习gradient descent的计算量太大,我们需要一个有效率的算法,即backpropagation

### 1. 链式法则

即微分中的连式法则

### 2. 反向传播

![image-20211230134831081](../pics/ML/image-20211230134831081.png)

我们有$z=x_1w_1+x_2w_2+b$
$$
\frac{\partial C}{\partial w} = \frac{\partial z}{\partial w}\frac{\partial C}{\partial z}
$$
这两个偏微分:

z/w称为forward pass,值即为fearture

,C/z称为backward pass,

![image-20211230135530634](../pics/ML/image-20211230135530634.png)

## 二. 编码

### 1. 数据集的构成

`Training data`: 由feature和真实值构成

`Testing data`: 由一组输入feature构成

### 2. 做作业

1. 检查training data的loss

   * 过大: **model bias**(通过增加feature)或者**没有optimization好**(比如local minima)
   * 如果更好,就检查testing data的loss

2. testing data loss过大:

   overfitting(过拟合退化,过于依赖训练资料)或者mismatch

   * 增加训练资料
   * **data augmentation** 根据已有资料,自己创造新的资料
   * **constrained** 人为增加约束

## 三. optimization失败的处理

### 1. critical point

梯度为0: `critical point`

* local minima
* `saddle point`鞍点

需要区分是哪一种critical point,因为saddle point比local minima更需要处理

#### 1.1 区分

取一个给定$\theta$附近的点,有泰勒展开: 

g是梯度向量,H是二阶导Hessian矩阵

![image-20211231204845308](../pics/ML/image-20211231204845308.png)

在critical point,g为0,中间这项去掉,即可根据H知道状态,即微积分学的

> H正定,为**local minima**
>
> H负定为极大值
>
> H有时大于0,有时小于0,是**saddle point**

#### 1.2 鞍点

在鞍点的时候,g为0,H指明前进方向

取**负值特征值**的特征向量u,让$\theta = \theta ' + u$即可

### 2. batch and momentum

#### 2.1 为什么要batch

<img src="../pics/ML/image-20220101145017824.png" alt="image-20220101145017824" style="zoom:50%;" />

我们是对每个batch的min L进行微分计算的,为什么要这么做?

1. `Shuffle`: 每个epoch之后都会进行shuffle,即重新分batch

2. `full batch`: 不分batch
3. 分batch即每次送入一个batch_size的数据进行训练,并更新参数

* 原因:
  * 在不分batch的情况下,我们每次更新参数都需要看所有的资料,但是准确(powerful)
  * 而分batch下只需要看batch_size大小的资料,但是不准确(noisy)
  * 然而,__noisy的结果更适合训练__

![image-20220101150419880](../pics/ML/image-20220101150419880.png)

#### 2.2 momentum

![image-20220101150708098](../pics/ML/image-20220101150708098.png)

一个球在物理上,从高处滚下来可能不会被saddle point和local minima卡住;

在实现上: 通过gradient方向 + 前一步方向 来移动

### 3. learning rate

当loss变小的时候,参数可能在山谷间来回的震荡,导致没有到达真正的minima

但是太大或者太小的learning rate都有问题,所以我们要`动态的learning rate`

* 原则:
  * 如果陡峭,希望learning rate小一点; 反之,希望learning rate大一点
* 改写:
  * ![image-20220101152303328](../pics/ML/image-20220101152303328.png)

