---
layout:     post
title:      设计模式-工厂模式
subtitle:   Design Pattern - Factory
date:       2020-05-17
author:     Jiahe Zhang
header-img: img/dp-back.png
catalog: true
tags:
    - 设计模式
    - 工厂模式
    - Design Pattern
    - Factory
---

## 1. 介绍

​		工厂模式是设计模式中最常见的模式之一，尤其在Java开发中，是一种提供创建对象的方式。也就是说用工厂模式，你无需自己new一个对象，直接问工厂拿就行，代码的可用性大大增加。一句话概括优点工厂模式便是无需对外提供new的具体函数。



## 2. 举例

​		一般纯粹介绍概念是没啥软用的，还是需要通过实际的代码来体现。接下来以一个汽车工厂CarFactory为例，介绍工厂模式的使用。

​		假设，我们社区定义了一种商品叫做Car，它需要加汽油，并且可以跑。而我打算开一个汽车店，想通过卖汽车来养家糊口。那么该怎么实现这个工厂，并提供给客户（端）Car呢？



### 2.1 接口定义

​		按照刚才说明，汽车要加油，能跑，那么有如下接口定义：

```java
public interface Car {
    void run();
    void addGas(int gas);
}
```

（实际的接口和功能肯定没有这么简单，这里一律从简，毕竟重点在于设计模式）



### 2.2 继承接口

​		有了统一的接口，那么我们就可以根据接口实现我们想实现的产品，也就是具体的汽车，为了迎合市场，我们打算造BMW和QQ这两种车，（简化了实际的功能实现）：

```java
public class BMW implements Car {
    private int gas;

    public void run() {
        if (gas >= 5){
            System.out.println("BMW run!");
            gas -= 5;//BMW油耗大
        }
    }

    public void addGas(int gas) {
        this.gas += gas;
    }
}
```



```java
public class QQ implements Car {
    private int gas;

    public void run() {
        if (gas >= 0){
            System.out.println("QQ run!");
            gas--;
        }

    }

    public void addGas(int gas) {
        this.gas += gas;
    }
}
```



### 2.3 造工厂

​		有了这两种具体类，其实客户（端）就可以直接自己new一辆Car出来，比如Car car = new BMW()， 但是这样做有一个缺点，我想更新这个汽车，可能内部更新为BMW2，那么客户端的代码就需要相应的做修改，十分不好。但是这种缺点可以通过工厂模式克服。我们定义一个汽车工厂：

```java
public class CarFactory {
    public static Car buyCar(String branch){
        if (branch.equals("QQ")){
            return new QQ();
        }
        else if (branch.equals("BMW")){
            return new BMW();
        }
        else {
            throw new NullPointerException();
        }
    }
}
```

​		这个工厂便可以根据客户的需求，提供不同的Car，也不需要自己去调用构造函数。



### 2.4 使用工厂

​		我们可以写个客户端测试代码来测试：

```java
public class Test {
    public static void main(String[] args) {

        try{
            Car car = CarFactory.buyCar("BMW");
            car.addGas(10);
            car.run();
        } catch (Exception e){
            System.out.println(e);
        }
    }
}
```

```
BMW run!
```

​		我们成功通过CarFactory这个工厂类购买到了BMW汽车！用户无需自己new一辆车！这里我们使用了静态工厂的方式，此外还有抽象工厂，也就是重新定义个工厂接口，这样能统一每一个工厂继承类。这时所有java文件如下图所示：
![image](https://github.com/JiaheZhang/JiaheZhang.github.io/blob/master/img/factory.jpg)