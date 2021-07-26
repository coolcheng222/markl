# Scheduled注解源码解读

## 一. 宏观的要点

1. 所有Scheduled使用同一个线程,至少在短时间内使用同一个线程
2. 使用线程阻塞的方式来执行定时任务,阻塞到下一个任务开始

## 二. EnableScheduling注解

### 1. 引入的组件

`SchedulingConfiguration`组件

### 2. SchedulingConfiguration

是一个__配置类__,引入了` ScheduledAnnotationBeanPostProcessor`

### 3.  ScheduledAnnotationBeanPostProcessor

该方法是个接口大杂烩

```java
public class ScheduledAnnotationBeanPostProcessor
      implements ScheduledTaskHolder, MergedBeanDefinitionPostProcessor, DestructionAwareBeanPostProcessor,
      Ordered, EmbeddedValueResolverAware, BeanNameAware, BeanFactoryAware, ApplicationContextAware,
      SmartInitializingSingleton, ApplicationListener<ContextRefreshedEvent>, DisposableBean {
```

#### 3.1 接口

* 后置处理器

  ```java
   MergedBeanDefinitionPostProcessor, DestructionAwareBeanPostProcessor,
  ```

* aware

  ```java
  EmbeddedValueResolverAware, BeanNameAware, BeanFactoryAware, ApplicationContextAware,
  ```

* 单例

  ```java
  SmartInitializingSingleton
  ```

* 事件监听器

  ```java
   ApplicationListener<ContextRefreshedEvent>
  ```

* 可以销毁

  ```java
  DisposableBean
  ```

* 保存定时任务

  ```java
  ScheduledTaskHolder
  ```


#### 3.2 构造器

构造器new了一个ScheduledTaskRegistrar并保存到registrar属性

## 二点五. 自动配置类

```java
TaskSchedulingAutoConfiguration 
```

这是一个自动配置类,负责构造并将ThreadPoolTaskScheduler放进容器

## 三. 任务注册

### 1. 概述

该过程在refresh的`finishBeanFactoryInitialization`阶段,即创建所有bean的时候,

后置处理器发挥作用注册Scheduled task的过程

后置处理器即` ScheduledAnnotationBeanPostProcessor`

### 2. 后置处理

该后置处理器检测拥有`@Scheduled`,`@Schedules`的方法,并注册

```java
processScheduled(Scheduled scheduled, Method method, Object bean)
    //第一个param就是注解及其参数
```

> **processScheduled**:
>
> 创建一个跟bean和method有关的runnable
>
> 经过对cron的解析以后放进tasks(一个放task的set)
>
> ```java
> tasks.add(this.registrar.scheduleCronTask(new CronTask(runnable, new CronTrigger(cron, timeZone))));
> ```
>
> scheduleCronTask方法将cronTask放进registrar的cronTasks中

## 四. 注册任务/线程

### 1. 注册流程

在refresh的finishRefresh阶段,触发ContextRefreshedEvent事件,ScheduledAnnotationBeanPostProcessor作为事件监听器调用其方法:

> __finishRegistration__: 
>
> 此时ScheduledTaskRegistrar已经获取所有的定时任务并保存, 我们的任务是给他配置一个`TaskScheduler`,默认为`ThreadPoolTaskScheduler`
>
> 最后一步调用registrar.afterPropertiesSet--scheduleTasks();
>
> > __scheduleTasks__ -- scheduleCronTask:
> >
> > 该方法处理四种任务: triggerTasks,cronTasks,fixedRateTasks,fixedDelayTasks
> >
> > 我们的任务正如前面所说放在cronTasks中
> >
> > 其会先将任务从未处理任务中拿出
> >
> > 然后交给`ThreadPoolTaskScheduler`处理
> >
> > > 