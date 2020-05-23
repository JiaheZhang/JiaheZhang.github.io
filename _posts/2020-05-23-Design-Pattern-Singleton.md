---
layout:     post
title:      设计模式-单例模式
subtitle:   Design Pattern - Singleton
date:       2020-05-23
author:     Jiahe Zhang
header-img: img/dp-back.png
catalog: true
tags:
    - 设计模式
    - 单例模式
    - Design Pattern
    - Singleton
---

## 1. 介绍

​    什么是单例模式？单例模式是指一个类仅有一个实例，并提供其他类获得这个实例的方法。比如频繁地创建和销毁数据库的连接很费资源，如果所有的数据库连接都使用同一个实例，那样便会比较合理。单例模式又有饿汉模式、懒汉模式、和双检锁模式，其实理解不难，都挺相近，无非是线程安全的区别。


## 2. 例子
​    我们以一个实际例子，深入讲解单例。假设一些卖票机子，要向公众卖票，在每次卖票之前，需要整理一下机器，从而卖下一张，假设用一个Prepare类用于表示这个功能，而这个类使用单例模式中的**懒汉模式**，大大减少内存的使用，创建和销毁的开销。

```java
class Prepare {
    private static Prepare prepare;

    private Prepare() {}

    public static Prepare getInstance() {
        if (prepare == null){
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            prepare = new Prepare();
        }
        return prepare;
    }

    public void displayLog(String name) {
        System.out.println(name + " is prepare " + this.toString());
    }
}
```

看看到懒汉模式中，Prepare类事先没有初始化，而是在有人想要时才去创建，减少开销，所以称为“懒汉”。这里我们假设new一个很耗时，也很占内存。这样的懒汉模式优点便是降低内存使用，而这样写便会产生线程不安全。请往下看。

```java
class SellTicket {
    private int num = 100;

    public  void sell(String name) {
        while (true) {
            //单例
            Prepare prepare = Prepare.getInstance();
            prepare.displayLog(name);
            synchronized (this) {
                if (num > 0) {
                    System.out.println(name + " sell ticket at " + " " + num);
                    num--;
                } else {
                    break;
                }
            }
        }
    }
}
```

我们写一个多线程来运行他们：

```java
public class demo {
    public static void main(String[] args) {
        SellTicket sellTicket = new SellTicket();
        new Thread(()->sellTicket.sell("机器1")).start();
        new Thread(()->sellTicket.sell("机器2")).start();
    }
}
```

```
机器1 is prepare singleton.Prepare@2858c11c
机器1 sell ticket at  100
机器2 is prepare singleton.Prepare@2770f418
机器2 sell ticket at  99
机器1 is prepare singleton.Prepare@2858c11c
机器1 sell ticket at  98
.....
```

可以从输出看到，一开始时Prepare被初始化了两次，一个是@2858c11c，另一个是@2770f418，已经违反单例的原则了，为什么会这样呢？在实例化Prepare时，我们对进程休眠了1000ms，用于假设new时的开销。如果这个时候有两个线程同时想要获得Prepare的实例，恰巧同时通过了判空的条件，于是便new了两个。这便是懒汉模式的线程不安全。要想获得线程安全只要加个锁便可：

```java
public static synchronized Prepare getInstance() {...}
```

​    另一方面，**饿汉模式**的写法也很简单，

```java
class Prepare {
    private static Prepare prepare = new Prepare();

    private Prepare() {}

    public static Prepare getInstance() {
        return prepare;
    }

    public void displayLog(String name) {
        System.out.println(name + " is prepare " + this.toString());
    }
}
```

​    无论是饿汉模式还是懒汉模式，都有各自的优缺点。饿汉模式会事先创建好实例，不管有没有人来调用，浪费了内存；而懒汉模式为了线程安全，每次都要加锁解锁，降低性能。于是便有了**Double Check Locking** **双检查锁机制**：

```java
public static Prepare getInstance() {
        if (prepare == null){
            synchronized (Prepare.class) {
                if (prepare == null) {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    prepare = new Prepare();
                }
            }
        }
        return prepare;
    }
```

写成以上形式即可，在判空成立后获得锁，若还是没有被实例化，那么再去new。因为已经事先获得了锁，防止线程不安全产生。

## 3. 总结

​    我们用一个简单的例子实现了单例模式，包括懒汉模式、饿汉模式和双检查模式。这种都有各自的优缺点，推荐使用双检查模式的单例模式，虽然实现起来比较复杂，但是能一定程度上解决其他两者遇到的问题。