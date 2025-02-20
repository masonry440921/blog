---
title: factory pattern
published: 2025-02-20
description: ""
tags: [design]
category: design pattern
---



## 工程模式和抽象工厂模式是两种不同的设计模式，主要区别在于它们的用途和实现方式。

### 1. 工程模式（Factory Pattern）
**用途**：用于创建对象，隐藏具体类的实例化过程，客户端只需通过工厂接口获取对象。

**实现方式**：
- 定义一个工厂接口或类，包含创建对象的方法。
- 具体工厂类实现该接口，负责创建具体对象。

**优点**：
- 解耦客户端与具体类。
- 便于扩展新的产品类。

**缺点**：
- 每增加一个产品类，可能需要修改工厂类。

**示例**：
```java
interface Product {
    void use();
}

class ConcreteProduct implements Product {
    public void use() {
        System.out.println("Using ConcreteProduct");
    }
}

class Factory {
    public Product createProduct() {
        return new ConcreteProduct();
    }
}
```

### 2. 抽象工厂模式（Abstract Factory Pattern）
**用途**：用于创建一系列相关或依赖的对象，提供一个接口来创建多个产品族。

**实现方式**：
- 定义抽象工厂接口，包含多个创建产品的方法。
- 具体工厂类实现该接口，负责创建一组相关产品。

**优点**：
- 保证产品族的兼容性。
- 便于切换产品族。

**缺点**：
- 扩展新产品族时，需修改抽象工厂接口及其实现类。

**示例**：
```java
interface AbstractProductA {
    void use();
}

interface AbstractProductB {
    void use();
}

class ConcreteProductA1 implements AbstractProductA {
    public void use() {
        System.out.println("Using ConcreteProductA1");
    }
}

class ConcreteProductB1 implements AbstractProductB {
    public void use() {
        System.out.println("Using ConcreteProductB1");
    }
}

interface AbstractFactory {
    AbstractProductA createProductA();
    AbstractProductB createProductB();
}

class ConcreteFactory1 implements AbstractFactory {
    public AbstractProductA createProductA() {
        return new ConcreteProductA1();
    }

    public AbstractProductB createProductB() {
        return new ConcreteProductB1();
    }
}
```

### 总结
- **工程模式**：用于创建单一对象，隐藏实例化细节。
- **抽象工厂模式**：用于创建一系列相关对象，确保产品族的兼容性。

两者各有适用场景，选择时需根据具体需求决定。