# 组合数学

> [TOC]

## 一. 绪论

### 1. 研究的内容

> * 存在性问题
> * 计数问题
> * 构造性问题
> * 优化问题

### 2. 幻方

在n乘n的放入1-n*n,以保证 __每行,每列,每条对角线__上的和相等,这样的称为幻方

* __幻和s__:行列的整数和称为幻和
  $$
  s=\frac {n*(n^2+1)}2
  $$

* __存在性__: 对于任何`n`>=3,都存在n阶幻方

#### 2.1 构造n阶幻方

* __奇数阶幻方的构造方法__:
  * 首先在第一行的中间置`1`
  * 向右上角逐个填充数字
  * 若下家被填充,则从自己的__下方__继续填充,并回到上一步

----------

## 二. 排列组合

排列组合的定义我们都知道了.

* __排列(Permutation)__:$P(n,r) = \frac {n!}{(n-r)!}$
* __组合(Combination)__:$C(n,r)=\frac{n!}{(n-r)!r!}$

### 1. 排列的递推关系

* __分布递推__: 
  $$
  P(n,r)=nP(n-1,r-1)
  $$

  * 理解: 将第一个盒子固定,剩下的即n-1个盒子放r-1的排列问题

* __分类递推__:
  $$
  P(n,r)=P(n-1,r)+rP(n-1,r-1)
  $$

  * 理解: 不选择第一个球,则余下是n-1个球放入r个盒子;选择第一个球,则r种情况之下,n-1个球放入r-1个盒子

### 2. 组合的性质

1. $C(n,r)=C(n,n-r)$
2. $C(n,l)C(l,r)=C(n,r)C(n-r,l-r)$
   * 理解: 
   * n位同学,l个班委,在班委中选出r个为核心(左边)
   * n位同学选出r个核心,在从n-r中选出l-r个为其他班委
3. $C(n,0)+...C(n,n)=2^n$
4. $C(n,0)-C(n,1)+...+C(n.n)=0$

#### 2.1. 格路模型

* __格路模型__:从坐标原点(0,0)到指定某点(m,n),只能选择横走或竖走,每次走一格.

* 解:
  $$
  已知,总的步数为m+n\\
  看做一个m+n长度字符串
  \\有n个y需要填充\\
  答案是C(m+n,n)种
  $$

#### 2.2 二项式定理

对于二项式展开每一个通项$a^{n-r}b^r$,都有从n个b中挑选r个b的过程,故是一个组合过程,其系数为$C(n,r) $

#### 2.3 递推关系

* $C(n,r)=C(n-1,r)+C(n-1,r-1)$
  * 理解: 杨辉三角形就是格路问题的延伸,对于格路问题的最后一步,固定是一横,则方法为C(n-1,r),一竖,则C(n-1,r-1)

    ![image-20200407140806780](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200407140806780.png)

* $$
  C(m+n,r)=C(m,0)C(n,r)+C(m,1)C(n,r-1)+ ...C(m,r)C(n,0)
  $$

  * __范德蒙德恒等式__
  * 理解: m个红球,n个蓝球,取共r个,那就从m和n中一个一个列举,加起来就是右边

--------

### 3. 特殊排列



#### 3.1 圆排列 

* __思想__: 圆排列从某点剪开即线排列,然后除以剪开的情况数
  $$
  从n个中取r个的圆排列数为\\
  P(n,r)/r,n\ge r\ge2
  $$

#### 3.2 项链排列

与圆排列相似,但翻转后的排列和原排列是同一种情况.
$$
从n个中取r个的项链排列数为\\
P(n,r)/2r,r\ge3
$$

-----

### 4. 多重排列

在某些情况下,元素的个数不仅可以重复,而且个数有限.称为__多重排列__.其__全排列__一般记作
$$
P(n;r_1,r_2...r_t)\\
或者\\
\begin{pmatrix}
n\\
r_1\ r_2...r_it
\end{pmatrix}
$$
n表示总个数,r~i~表示各个元素的重复个数

* __思想__: 各个重复元素加上下标加以区别.如$P_1P_2n_1n_2g_1g_2ia$的全排列,然后除去各个重复的排列

* 解:
  $$
  P(n;r_1,...r_t)=\frac {n!}{r_1!r_2!...r_t!}
  $$

#### 4.1 多项式系数

设有$(a_1+...a_t)^n = ?$

* __思想__:根据多重排列思想,假设每一项有$r_1$个$a_1$,$r_2$个$a_2$...则其通项系数为这样的多重全排列的解

* __解__:
  $$
  a_1^{r_1}a_2^{r_2}...a_t^{r_t}的系数为:\\
  \frac {n!}{r_1!r_2!...r_t!}
  $$
  

#### 4.1 隔板法

* 引例: 标号1-9的乒乓球进入6个相同的洞中,有多少种进法

* __解1__: 使用隔板法,假设有5个隔板把空间分成六份,则__隔板和球__的顺序形成一个__多重排列,__其中$r_{隔板}=5$,其余都为1.

  答案: 14!/5!

* __解2:__ 在第一个球放下以后(6种),球本身变成一个隔板,所以第二个放下(7种),以此类推.

  答: 6\*7\*...14 = 14!/5!

------------

### 5. 可重组合

* __多重集__: 元素可以多次出现的集合

* __可重组合__: 从A={1,2,3...n}中,取r个元素{$a_1,a_2...a_r$},允许$a_i=a_j,i\ne j$,个数记为==$\overline C(n,r) $==

* __模型__: 取r个无标志的球,n个有区别的盒子,每个盒子可以放任意多个球(包括0)

* __思想__: 给r个取出的数__排序后__分别标上数字 $0,1,...r-1$,假设本身n个样本记作1,...n,__样本本身和序号相加__记为新的n+r-1(序号从1到n+r-1,不重复)个样本,由此新样本进行无重组合,即可重组合的解

* __解__:
  $$
  \overline C(n,r) = C(n+r-1,r)
  $$

* __隔板法理解__

  n个取r个就是拿隔板分n个区域(n-1个隔板)

  所以一共就是n+r-1个元素选n-1个作为隔板,C(n+r-1,r)

  ---

例题: 线性方程 $x_1+...x_n=b$的非负整数解个数?

解:  世界上有b个1,需要分成n个区域,隔板理解的答案就是 $C(n+b-1,b)$

------------

### 6. 不相邻组合

A中包含了n个元素{1,2,3...n},取出r个不相邻(且不可重复)的组合,即不存在任何相邻的组合,称为__不相邻组合__

* __思想__:
  * 设$B=\{b_1,b_2...b_r\}$是一组不相邻组合,给每个元素排序并从0到r-1标号
  * 新构造的序列为 : $b_r-标号$
  * 这个新的序列序号从$1到n-r+1$,故有这么多样本,取出r个
  * 答案为==$C(n-r+1,r)$==
  *  _附证_:因为不相邻不可重,所以新构造序列不会出现重复

#### 6.1 秘钥分配问题

* 假设有7个人分配钥匙,一把钥匙对应一个锁,要求至少4人到场时才能打开所有锁(且任意四人一定能打开所有锁)
  * 问: 至少多少把锁
  * 问:每个人至少多少把钥匙
* __多少把锁 思想__:
  * 已知3人到场时不能开锁,则其必缺少至少1把钥匙
  * 且任意三人组合缺乏的钥匙不能重复,否则两个组合配对四人,也打不开
  * 故每个三人组合都对应着至少一把独特的缺失钥匙
  * 锁(钥匙种类)的数目则至少为 ==C(7,3)=35==

* __多少钥匙 思想__:
  * 从上面的思想继续考虑
  * 每个人都要对剩下6个人形成的3人组负责
  * 对于3人组产生的独特缺失钥匙,它都要有
  * ==C(6,3)==

------------

### 7. 总结

![image-20200409152648969](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200409152648969.png)

---------

## 三. 全排列生成算法

### 1. 递归算法

在n-1排好的基础上,插入第n个元素即可.时间空间复杂度大

-----

### 2. 字典序法

* 任何n个字符集的排列都可以和1-n的n个数字排列一一对应,n个数字的全体排列之间存在一个确定的线性关系.

* __特点__: 

  * 每个排列的后继都可以从他的前驱经过最小的变化而得到

  * 是从第一个排列开始生成所有排列的方法

  * 保持尽可能长的共同前缀下,变化限制在尽可能短的后缀上(变大)

  * 最后的结果是从右至左递减

    ```mathematica
    123
    132
    213
    231
    312
    321
    ```

* __想法__:

  ​	从右向左扫描,出现递减(a<b)则说明可以优化(两元素交换,选择a和比a大的偏小的元素交换)

  ,保证当前交换数后的后缀最小

------

### 3. SJT算法

* __可移动数(mobile integer)__: {1,2,...n}排列中的每个数字具有一定的移动方向.

  	* 如果该数方向指向的相邻数比该数小则为可移动数
  	* 1永远不可移动
  	* max永远可以移动,除非遇到端点

* __思想__: 先给所有数指明方向为 `向左`
  $$
  1234 
  $$

  * 寻找最大的可移动数,向方向移动

    ```mathematica
    <1 <2 <3 <4
    <1 <2 <4 <3
    <1 <4 <2 <3
    <4 <1 <2 <3
    ```

  * 任何数字交换以后,比他大的数字方向变换

    ```math
    >4 <1 <3 <2
    ```

  * 以此类推

    ```math
    <1 >4 <3 <2
    <1 <3 >4 <2
    <1 <3 <2 >4
    
    <3 <1 <2 <4
    ...
    ```

    

--------

### 附. stirling数

$n!\sim \sqrt{2\pi n}(\frac {n}{e})^n$

------

## 四. 母函数

* __定义__: 对于序列$C_0,C_1...$构造一函数
  $$
  G(x)=C_0+C_1x+C_2x^2
  $$
  称G(x)为序列$C_0,...$的__母函数__,我们通常只关注这个序列而不是函数本身.

* __地位__:母函数是组合数学中最常用的工具

* 引例:骰子总和问题: 若有m粒骰子,要求投出的和为n,能几种方案?
  *  从2个骰子,点数和为6开始思考
  * 两个骰子之间毫无疑问是__乘法原理__
  * 但单个骰子如何使用加法原理?
  * 假设每个点出现的概率固定为x
  * 则一个骰子出现i个点的概率为$x^i$
  * 对点数1-6使用加法原理 得$x+x^2+x^3+...x^6$
  * 应用乘法原理$(x+x^2+x^3+...x^6)^2$,所得$x^6$的**<u>系数</u>**即和为6的情况数
  * 可以推广到一般形式
* __本质__: 是一种映射关系,蕴含了乘法和加法法则.

-------

## 五. 整数拆分

将正整数拆分为若干正整数之和

分为有序拆分(composition)(考虑排列)和无序拆分(partition)(只考虑组合)

### 1. 有序拆分

r-1个隔板分n个球就行了,一般有序拆分会规定r个数字,且不为0.

### 2. 无序拆分

无序拆分数通常用$p(n)$表示,即n可以拆分成多少种时正整数的和,不规定数字的个数.

$p(3)=3$

母函数: G(x) = (1+x+x方..)(1+x方...)...中x^n的系数

### 3. ferrers图像

将n个数字表示成n个格子:,上一层比下一层多,每一层一个格子,翻转以后性质不变

* 最大数字为k,和拆分成k个数相等

---------

## 六. 递推关系(差分方程)

* 引例:汉诺塔
  * 你递归也会用吧 $a_n = 2a_{n-1}+1$

### 1. 用母函数解决递推关系

* 以汉诺塔为例:

$$
\begin{aligned}
&设汉诺塔序列为h(1),h(2)...
\\&构造母函数H(x)=h(1)x+h(2)x^2...\\
&H(x)=h(1)x+h(2)x^2...,h(n)=2h(n-1)
+1\\
\\
& H(x)-2xH(x) = h(1)x+[h(2)-2h(1)]x^2+...
\\&(1-2x)H(x) = x+x^2+...=\frac {x}{1-x}
\\&H(x) = \frac x{(1-2x)(1-x)}
\\如何从母函数得到序列?
\\& 化为部分分式: H(x) =\frac A{1-x}+\frac B{1-2x}
\\&解得 A=-1,B=1
\\&H(x) =\frac 1{1-x}-\frac 1{1-2x}
\\&H(x)展开成幂级数为\Sigma_{k=1}^{\infty}(2^k-1)x^k;
\\&h(x)=2^k-1
\end{aligned}
$$

### 2. 偶数5问题

在n位数中包含偶数个5,有多少种方案

$a_n记为n为十进制数中出现偶数个5的个数\\b_n记为n位十进制数中出现奇数个5的个数$
$$
已知\\
a_n=9a_{n-1}+b_{n-1},a_1=8\\
b_n=9b_{n-1}+a_{n-1},b_1=1
$$

----------

## 七. 线性常系数其次递推关系

### 1. 斐波那契

F(0)= 0



* 恒等式: 
  * $F_1^2+...+F_n^2=F_nF_{n+1}$

### 2. 线性常系数齐次递推关系

若有$a_n+c_1a_{n-1}...+c_ka_{n-k}=0$, 有$a_0=d_0...a_{k-1}=d_{k-1}$初始值,称此为__k阶线性常系数齐次递推关系__

* __母函数方法__
* __特征多项式__:

![image-20200417132146341](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200417132146341.png)

通解为$A a_1^n+Ba_2^n$

* __有重根__:

(A+Bn+...$Zn^k$)$a^n$

* 有复根:
  * ![image-20200423134930237](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200423134930237.png)

-----

## 八. 卡特兰数

### 1. 概述 

n个数字依次入栈,穿插着出栈,有多少种出栈序列?结论记为__卡特兰数__.

* 思考:

  * 第一次为空时有k个元素出栈,即1出栈的序号
  * 那就分为1\~k-1和k+1\~n(n-k个元素)的序列

  结论:$f(n)=f(k-1)*f(n-k),k=1\sim n$

* 应用: 
  * n各节点构成二叉树有多少情形
  * 正n变形用顶点划分成不同三角形的分法

### 2. 直接表达式

