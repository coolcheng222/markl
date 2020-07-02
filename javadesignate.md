# 设计模式/七大原则

> [TOC]

## 一. 绪论

### 1. 重要性

软件工程中,设计模式(design-pattern)是对软件中**普遍存在(反复出现)**的各种问题提出的**解决方案**

是从建筑领域引入计算机的.

* **代码重用性**(相同功能不用多次编写)
* **可读性**(编程规范性)
* **可扩展性**
* **可靠性**
* **高内聚,低耦合**



------------------

# 七大原则

## 一. 七大原则

设计 设计模式 的七大原则

> * ==单一职责原则==
> * ==接口隔离原则==
> * ==依赖倒转原则(倒置)==
> * ==里氏替换原则==
> * ==开闭原则(OCP)==
> * ==迪米特法则==
> * ==合成复用原则==

核心思想:

* 找出可能变换之处,不和不需要变化的混在一起
* 针对接口编程,而不是针对实现
* 为了交互对象之间松耦而努力

## 二. 单一职责原则

`single responsibility principle`

### 1. 理念

对类来说,每个类应该只负责一个职责.

如果A负责不同职责: 1,2. 因为职责1改变A会使得职责2出问题.

所以应当把A的粒度分解为A1,A2

### 2. 实例

* __反例__:

  ![image-20200619195755401](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200619195755401.png)
  * 承担多种交通工具都在公路上跑的职责,违反单一职责原则

  * ```java
    class Vehicle{
        public void run(String vehicle){
            System.out.println(vehicle + "在公路上运行...");
        }
    }
    ```

* 改进的代码1

  ```java
  class RoadVehicle{
      public void run(String vehicle){
          System.out.println(vehicle + "在公路上运行...");
      }
  }
  class AirVehicle{
      public void run(String vehicle){
          System.out.println(vehicle + "在空中上运行...");
      }
  }
  class WaterVehicle{
      public void run(String vehicle){
          System.out.println(vehicle + "在水中上运行...");
      }
  }
  ```

  * 符合单一职责原则
  * 但是需要修改客户端,不好

* 改进的代码2:

  ```java
  class Vehicle2{
      ublic void run(String vehicle){
          System.out.println(vehicle + "在公路上运行...");
      }
      public void runFly(String vehicle){
          System.out.println(vehicle + "在公路上运行...");
      }
      public void runWater(String vehicle){
          System.out.println(vehicle + "在空中上运行...");
      }
  }
  ```

  * 改进开销不是很大
  * 在类层面没有实现单一职责原则,但在方法层面实现了

### 3. 注意事项和细节

1. 降低类的复杂度,一个类只负责一项职责
2. 提高类的可读性,可维护性
3. 降低变更带来的风险
4. 只有在代码实在简单的时候才能违反这个原则,不然最好保持单一职责

----------

## 二. 接口隔离原则

`Interface Segregation Principle`

 ### 1. 理念

客户端不应该依赖它不需要的接口,即一个类对另一个类的__依赖(使用)__应该建立在最小的接口上

### 2 理解问题

>  比如B实现了接口1的5个方法,A依赖了B(A中方法需要用到B),但只要用到B中的3个方法,违反了接口隔离原则
>
> 所以此时需要将Interface1拆小,只和他们需要依赖的接口产生依赖关系

> A通过接口1依赖B,如果接口对于A来说不是最小接口,**那B必须额外实现不需要的方法**,这并不是很好的体现

![image-20200620201932672](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200620201932672.png)

### 2 改进类图



![image-20200620203037201](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200620203037201.png)

将接口拆分以达到__最小__的目的

## 三. 依赖倒转原则

`Dependence Inversion Principle`

### 1. 理念

1. 高层模块不应该依赖低层模块,二者都应依赖于抽象(抽象类,接口)
2. 抽象不应该依赖细节(实现类),细节需要依赖抽象
3. 中心思想就是面向接口编程
4. 相对于细节的多变,抽象的东西要稳定得多.
5. 抽象类和接口制定规范,细节交给实现类

### 2. 理解问题

考虑如下类:

```java
class Person{
    public void receive(Email email){
        System.out.println(email.getInfo());
    }
}
```

* 问题在于:
  * 方法直接参数**传入了一个类对象**(对具体类的依赖),是一个细节实现类,如果需要新增短信类,会__影响扩展性__

### 3. 解决

引入一个**接口**(抽象),让People对这个接口产生依赖

```java
class Person{
    public void receive(Ireceive ir){
        System.out.println(ir.getInfo());
    }
}
```

### 4. 三种方法

* __接口传递依赖__:

  * 用类实现接口,方法参数多态调用

* __通过构造方法依赖传递__

  * 依然是接口,但用的不是参数多态

    ```java
    interface ITV{
        void play();
    }
    class OpenandClose{
        ITV tv; //接口作为实现类的属性
       
        public OpenandClose(ITV tv){
       		 this.tv = tv;//在构造器传入实现类对象
        }
        public void open(){
            tv.play();//用属性调用
        }
    }
    ```

* __通过setter方法实现__

  * 也是把接口当成属性,然后用setter设置具体对象

## 四. 里氏替换原则

`Liskov Substitution Principle`

### 1. 继承的问题

继承包含这样一层含义: 父类凡是定好的方法,实际上是在**设定规范和契约**,虽然不强制要求所有子类必须遵守这些契约,但是如果对已经实现的方法任意修改,就会对整个继承体系**造成破坏**

* 继承在给程序带来便利的同时,带来了弊端.:
  * 增强了程序的侵入性,降低移植性
  * 增加对象耦合性
    * 如果一个类被继承,修改时需要考虑所有子类

### 2. 理念

* __里氏替换原则__: <u>如果</u>对于每个类型为T1的对象O1,<u>都有</u>类型为T2的对象O2,<u>使得</u>以T1定义的所有程序P在所有对象o1都代换成o2时,程序p的行为没有发生变化,<u>那么</u>T2是T1的子类型
  * 所有引用基类的地方必须能透明的使用其子类对象

* 理解:
  * 使用继承时,尽量不要重写父类方法
  * 继承会让耦合性增强,适当情况下可以用__聚合,组合,依赖__解决问题



## 五. 开闭原则(OCP)

`Open Close Principle`

### 1. 理念

开闭原则编程中最基础最重要的设计原则

* __概念__: 一个软件实体比如类,==对拓展开放,对修改关闭==.用抽象构建开放,用事先拓展细节.

实现和依赖倒转很像

## 六. 迪米特法则

`Demeter Principle`

### 1. 理念

也叫最少知道原则

* 一个对象应该对其他对象保持最少的了解
* 类与类关系越密切,耦合越大
* 一个类对自己依赖的类知道的越少越好,即不管被依赖的累多么复杂,都应尽量逻辑封装在类的内部.对外除了public方法不透露任何信息
* **陌生类最好不要以局部变量出现**

## 七. 合成复用原则

`Composite Reuse Principle`

### 1. 理念

尽量使用合成/聚合,少继承

--------

# UML类图

UML就是统一建模语言,有一套符号用于描述各个元素和他们的关系

## 一. 类间关系概述

* __依赖__: ![image-20200626205019133](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200626205019133.png)

使用关系,代表A使用了B

* __关联__:

  <img src="C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200626205121544.png" alt="image-20200626205121544" style="zoom:50%;" />

  表示**一对一,多对一,一对多**关系

* __泛化__(继承):<img src="C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200626205245652.png" alt="image-20200626205245652" style="zoom:50%;" />

  表示A类继承B类

* __实现__:![image-20200626205531174](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200626205531174.png)

  表示实现接口

* __聚合__: 关联关系的特例,如果A有属性B类且B类属性通过set方法传入称为聚合

  ![image-20200626205653041](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200626205653041.png)

* __组合__: 关联关系的特例,B类是A类的属性.A初始化时B也被初始化
  
  * b指向A
* ![image-20200626205741108](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200626205741108.png)

## 二. 依赖

A对B的依赖,A使用了B(是属性,是参数,是返回值),并且如果B没有定义则无法度过编译

所以UML中是A指向B

## 三. 泛化

是依赖的特例,A泛化B暗含A对B的依赖,实际就是继承

所以A指向B

## 四. 实现

是依赖的特例,A实现B接口暗含A对B的依赖

A指向B



## 五. 关联

关联关系就是类与类的联系,是依赖关系的特例

* 具有导航性: 有双向或单向
* 具有多重性:  
  * 1: 有且仅有一个
  * 0...: 表示0个或多个
  * 0,1表示0个或1个
  * n..m: 表示n到m个都可以
  * m..: 至少m个

<img src="E:\markd\pics\image-20200628211630560.png" alt="image-20200628211630560" style="zoom: 33%;" />

<center>单向一对一(左连接,一个人可以有卡也可以没有)</center>

## 六. 聚合关系

是整体和部分的关系,这个整体和部分__可以分开__,是关联关系的特例

也具有导航性和多重性

一般B类是作为属性出现,A指向B

一般使用setter设置B属性,表现可分离的特点

## 七. 组合

是整体和部分的关系,这个整体和部分__不可以分开__,是关联关系的特例

一般B类是作为属性出现,A指向B

一般在A初始化时即初始化B,体现不可分离

如果有级联删除(A删时对应B也删),那也叫组合关系



# 设计模式(上)

## 一. 二十三种设计模式

> **创建型模式:** 单例模式,抽象工厂模式,原型模式,建造者模式,工厂模式
>
> **结构型模式:** 适配器模式,桥接模式,装饰模式,组合模式,外观模式,享元模式,代理模式
>
> **行为型模式:** 模板方法模式,命令模式,访问者模式,迭代器模式,观察者模式,中介者模式,备忘录模式,解释器模式,状态模式,策略模式,职责链模式

## 二. 单例模式

`Singleton`

采取一定方法保证在整个软件系统中,对某个类**只能存在一个对象实例**,并且该类只提供一个获取其对象实例的方法

单例模式有__八种方式__:

> 2个饿汉式
>
> 3个懒汉式
>
> 双重检查
>
> 静态内部类
>
> 枚举

### 1. 静态常量饿汉式

> 1. 构造器私有化
> 2. 类的内部创建静态对象(私有)
> 3. 向外暴露一个静态的公共方法

```java
class Singleton{
    //私有化构造器
    private Singleton(){

    }

    private final static Singleton instance = new Singleton();

    public static Singleton getInstance(){
        return instance;
    }
}
```

* 优点: 实现简单,在类装载的时候实现了实例化,避免了线程安全问题
* 缺点: 实例化太早,没有懒加载(lazy loading)效果,可能造成资源浪费

### 2. 代码块饿汉式

和上面就差一个代码块

> 1. 构造器私有化
> 2. **静态代码块**的内部创建静态对象(私有)
> 3. 向外暴露一个静态的公共方法

```java
class Singleton2{
    //私有化构造器
    private Singleton2(){

    }

    private final static Singleton2 instance; //可以用final

    static{
        instance = new Singleton2();
    }

    public static Singleton2 getInstance(){
        return instance;
    }
}
```

* 优缺点: 同上

### 3. 线程不安全懒汉式

> 1. 私有化构造器
> 2. 声明自身类的静态属性
> 3. 在获得实例的方法中创建实例

```java
class Singleton3{
    private Singleton3(){

    }

    private static Singleton3 instance; //不能用final

    public Singleton3 getInstance(){
        if(instance == null){
            instance = new Singleton3();
        }
        return instance;
    }


}
```

* 优点: 懒加载
* 缺点: 线程不安全

### 4. 同步方法懒汉式

> 在上述情况下,把getInstance变成同步方法

```java
public class LazySynchronized {
}
class Singleton4{
    private Singleton4(){

    }

    private static Singleton4 instance;

    public synchronized  Singleton4 getInstance(){ //就加一个synchronized
        if(instance == null){
            instance = new Singleton4();
        }
        return instance;
    }
}
```

* 优点: 懒加载,线程安全
* 缺点: 效率太低,每个线程get的时候都不能同步执行