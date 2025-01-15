# Vision and image

[TOC]

## 一. image filtering(Convolution)

### 1. digital image

>  考虑一个函数f:R^2->R,`f(x,y)`提供了对应坐标的一个强度值

* digital image

`digital image` 是一个包含"intensity value"的grid(矩阵)

它是f的一个"离散(采样,量化)discrete(sampled,quantified)"版本

255=white.0=black

<img src="E:/markd/pics/Untitled/image-20230907153726191.png" alt="image-20230907153726191" style="zoom:50%;" />

### 2. Linear Filter

对于函数f可以进行数值计算,在这里我们讨论称为"Convolution(linear filter)"还有"Cross-Correlation"的计算

#### 2.1 图像处理有的问题

<img src="E:/markd/pics/Untitled/image-20230907155029280.png" alt="image-20230907155029280" style="zoom:50%;" />

#### 2.2 以噪音为例介绍

我们在降噪中考虑将一个"区域",maybe 3*3,进行一个__linear filter__以将其转换成同一个数字

制造一个`Kernel`3*3矩阵,该矩阵在ML中作为weight存在,将每一个位置对应的值相乘再相加就是最终结果

![image-20230907160444559](E:/markd/pics/vision/image-20230907160444559.png)

#### 2.3 Cross-Correlation

> H为Kernel,F为图像

$$
G=H\otimes F
$$

`Cross-Correlation`是粗暴地将对应的内容相乘再相加形成一个结果

> (i,j)是窗口的中心点位

![image-20230907160805130](E:/markd/pics/vision/image-20230907160805130.png)

#### 2.4 Convolution

>  H为Kernel,F为图像

$$
G=H*F
$$

![image-20230907161532318](E:/markd/pics/vision/image-20230907161532318.png)

看公式注意到,实际上也只是将图像和Kernel的对应中心反转了一下

* 卷积满足**commutative** and **associative**(交换律,结合律)
* 边缘图像点无定义,处理会留空

#### 2.5 高斯Gaussian Kernel

![image-20230907162423736](E:/markd/pics/vision/image-20230907162423736.png)

### 3. Edge Detection

#### 3.1 导数检测(derivative)

`边缘检测`首先基于derivative的大小(导数),对于一个离散的图像,使用finite difference代替

![image-20230907163617174](E:/markd/pics/vision/image-20230907163617174.png)

对应的,自然也有梯度的概念(gradient)$=(F_x,F_y)$

> 对于大多数图像,需要先**降噪**,再求导,不然太难了

#### 3.2 使用卷积实现导数



使用卷积实现导数,可以略过降噪的过程,直接进行边缘检测

一般使用`Sobel Operation`近似 gaussian kernel的导数,或者`Laplacian of Gaussian`

> 拉普拉斯算子是空间(偏)二阶导相加,旨在使得**值为0**的位置为边缘
>
> ![image-20230907165236044](E:/markd/pics/vision/image-20230907165236044.png)

![image-20230907164328641](E:/markd/pics/vision/image-20230907164328641.png)

![image-20230907164407439](E:/markd/pics/vision/image-20230907164407439.png)

# 二. 图像sampling和resizing

## 1. sampling

* 内涵解释:

sampling就是从图中取一些点,前文都是针对digital image的离散采样,这里针对的是连续的图像

### 1.1 uniform sampling(均匀采样)

给定一个连续图像函数定义为$f(x,y)$,其对应的采样图像为$f_s(m,n)$

> m,n是以x,y为中心的采样点

![image-20230915130936832](E:/markd/pics/vision/image-20230915130936832.png)

> inner product: 内积https://mathworld.wolfram.com/InnerProduct.html
>
> dirac's delta function: 一个原点附近以外基本为0的函数,总面积为1

Δx and Δy are vertical and horizontal sampling intervals. $f_{s,x}=1/Δx, f{s,y}=1/ Δy$ are vertical and horizontal sampling frequencies.

### 1.2 插值interpolation

**插值**是使用采样技术(也就是根据附近点)reconstruct图像的技术

假定重构后的图像为$\hat{f}(x,y)$

![image-20230915132620949](E:/markd/pics/vision/image-20230915132620949.png)

> h: 插值函数,一般来说是(spatal invariant)空间不变的,且只与采样距离有关
>
> ![image-20230915132757199](E:/markd/pics/vision/image-20230915132757199.png)

这个过程可以转换为__线性卷积__

![image-20230915133026716](E:/markd/pics/vision/image-20230915133026716.png)

> 卷积:
>
> <img src="E:/markd/pics/vision/image-20230915133208399.png" alt="image-20230915133208399" style="zoom: 67%;" />

<img src="E:/markd/pics/vision/image-20230915133713101.png" alt="image-20230915133713101" style="zoom:50%;" />

### 1.3 举例: 像素复制

也就是根据附近的点插值

![image-20230915134041207](E:/markd/pics/vision/image-20230915134041207.png)

### 1.4 平均采样filter

![image-20230915160349637](E:/markd/pics/vision/image-20230915160349637.png)

![image-20230915160356592](E:/markd/pics/vision/image-20230915160356592.png)

![image-20230915160408786](E:/markd/pics/vision/image-20230915160408786.png)

### 1.5 sample-and-hold filter

![image-20230915160509520](E:/markd/pics/vision/image-20230915160509520.png)

### 1.6 总对比

![image-20230915160615500](E:/markd/pics/vision/image-20230915160615500.png)

## 2. 采样频率/频域

在一维信号采样领域中,采样频率过低(低于奈奎斯特率)会导致频率降低,在图像采样中类似,这种情况称为"aliasing(混叠)"

![image-20230915142850113](E:/markd/pics/vision/image-20230915142850113.png)

低频的采样在图像中亦会导致失真

> 连续图像可以被傅里叶变换分解成高低频波,形成频域,reconstrution就是根据采样点形成不同频率的波形,同一维信号的采样一致

### 2.1 Nyquist 采样频率

**采样频率至少是信号最高频率的2倍**,这就是nyquist sampling rate

•fs,x ≥2fm,x, fs,y≥2fm,y

![image-20230915155157566](E:/markd/pics/vision/image-20230915155157566.png)

两个问题:

1. 信号没有频带限制: <u>使用prefilter采用cutoff frequency fc=fs/2</u>
2. sinc filer难以实现: 使用类似的简单filter

范式: 

![image-20230915155504911](E:/markd/pics/vision/image-20230915155504911.png)

## 3. resizing

###  3.1 down-sampling下采样

把大的采样成小的就是下采样

* 简单方法: average filter

  取一个区域的平均数作为采样结果

* 简单方法存在的问题:
  * 采样频率低于nyquist rate会发生aliasing
  * prefilter应该为低通滤波器,理想下频率应该为新采样频率的一半

* K因子下采样: 根据k的步频来采样

![image-20230915162054241](E:/markd/pics/vision/image-20230915162054241.png)

```matlab
>> fp=conv2(f,h) # prefilter
>> g=fp(1:L:M,1:L:N) # L: factor
```

### 3.2 up-sampling上采样

* 根据最近邻插值,也就是像素复制

O[m’,n’] = I[(int) (m + 0.5), (int) (n+ 0.5)] , m = m’/M, n=n’/M.

* 双线性插值

> 双线性是指: A->B->C,f(B) = af(A) + (1 - a)f(C)
>
> a是距离的权重

![image-20230915163512955](E:/markd/pics/vision/image-20230915163512955.png)

# 三. Color and texture

## TODO

# 四. Deep Learning

# 五. Image Segmentation

`Image Segmentation`是一种将图片中实体分离的技术，

Firstly，

![image-20230928152805879](E:/markd/pics/vision/image-20230928152805879.png)

但是碍于图像太简单，一般图像处理需要使用clust

![image-20230928152856776](E:/markd/pics/vision/image-20230928152856776.png)

而对于DeepLearning,这是一个classification问题

## 1. Sementic segmentation

这种方法把每一个pixel归类到某个实体(category label)

![image-20230928153854229](E:/markd/pics/vision/image-20230928153854229.png)

<center>全卷积层,可以考虑先downsampling再upsampling</center>

> 考虑ML regression方法,bounding box由**x,y,width,height**组成,作为最终的输出,但<u>是其数目通常难以估计</u>,所以一般不这么做
>
> 而作为分类问题,我们使用一个固定的window(CNN)来判断一个区域是否为一个实体,但是segmentation是依赖上下文的,所以并不好使
>
> 

考虑一种方法`region proposals`,先找到可能有物体的区域,一般能找的很快,区域成为`proposal`

然后我们将proposal都resize成统一的大小,丢进neural network(**R-CNN**)

---

### Fast  R-CNN

1. Rol Pooling
2. Region Proposal Network(RPN)

![image-20230928163253351](E:/markd/pics/vision/image-20230928163253351.png)

## YOLO

You only look once

将图像分成网格,给每个box regress出5个值(dx,dy,dh,dw,confidence),然后计算出每个classes的score

![image-20230928164005436](E:/markd/pics/vision/image-20230928164005436.png)

## Mask R-CNN

Bounding box(dection) is removed

Colored mask (segmentation)stay

![image-20230928164505133](E:/markd/pics/vision/image-20230928164505133.png)

## 六. Harris Corner Detection

### 1. Feature extraction

`Corners`和`blobs`分别寻找"角度"和"区块(有不同的属性)"

geometric invariance: 旋转,放大

photometric invariance: 曝光,亮度

* advantages
  * **locality:** robust to occlusion
  * **quantity**
  * **distinctiveness**
  * **efficiency**

* Approach
  * Feature detection
  * Feature descriptor
  * Feature matching
  * Feature tracking

## 七. blobs

Log,高斯的拉普拉斯算子,加入卷积可以识别blobs(圆形区域)

### 1, Scale Invariabt Feature Transform

SIFT



1. peak detection: 挑选最大值

2. roubust matchning technique:(Feature matching)
   * 处理视野中的变化
   * 处理illumination中的关键改变
   * 快和高效
   * 代码可用