---
layout:     post
title:      设计模式-组合模式
subtitle:   Design Pattern - Composite Pattern
date:       2020-06-20
author:     Jiahe Zhang
header-img: img/dp-back.png
catalog: true
tags:
    - 设计模式
    - 组合模式
    - Design Pattern
    - Composite Pattern
---


## 背景

组合模式把一组相似的对象当作一个单一的对象，经常用于树形结构，比如文件夹-文件目录。

和二叉树之类的不同，这种树形结构不固定，不仅可以包含基本元素，也可以包含容器（可以包含元素）。这里元素便是部分的概念，容器则对应整体。对于程序来说，保持元素与容器的一致性是十分必要的，可以大大优化和美化代码。当有了一致性之后，在遍历某个节点下所有的内容时可以直接采用递归的方式，而无需区分是什么，比如下面：

```java
void visitNode(Node){
  if(Node != null){
    //do visit
    List<Node> children = Node.getChildren();
    for(...)
    	visitNode(child);
  }
}
```

将容器对象和叶子对象进行递归组合，使得客户在使用的过程中无须进行区分，可以对他们进行一致的处理。若没有一致性，会更加复杂，因此在设计这个工程时，可以考虑采用组合模式。

## 设计

组合模式的关键便是元素和容器继承同一个接口。比如上文中的文件-文件夹结构，其中文件和文件夹都可以继承Node这个接口。

```java
public interface Node {
  void add(Node node);
  String visit();
  List<Node> getChildren();
}
```

对于文件

```java
public class Element implements Node {
  private String name;
  
  public void add(Node node){
    //不允许 throw exception
    return;
  }
  
  public String visit(){ 
    return name; 
  }
  
  List<Node> getChildren(){
    //no children
    return null;
  }
}
```

对于文件夹：

```java
public class File implements Node {
  private String name;
  private List<Node> children;
  
  public void add(Node node){
    children.add(node);
  }
  
  public String visit(){ 
    return name; 
  }
  
  List<Node> getChildren(){
    return children;
  }
}
```

有了这样的设计模式，便在一定程度上保持了部分与整体的一致性。