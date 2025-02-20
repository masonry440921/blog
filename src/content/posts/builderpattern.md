---
title: Builder pattern
published: 2025-02-20
description: " "
tags: [design]
category: design pattern
---

建造者模式（Builder Pattern）是一种创建型设计模式，它允许你逐步构建复杂对象。建造者模式通过将对象的构建过程与其表示分离，使得相同的构建过程可以创建不同的表示。

在Java中，建造者模式通常用于构建具有多个可选参数的对象，尤其是在构造函数的参数较多时，使用建造者模式可以使代码更加清晰和易于维护。

下面是一个简单的Java示例，展示了如何使用建造者模式：

```java
// 产品类
class Product {
    private String part1;
    private String part2;
    private String part3;

    public void setPart1(String part1) {
        this.part1 = part1;
    }

    public void setPart2(String part2) {
        this.part2 = part2;
    }

    public void setPart3(String part3) {
        this.part3 = part3;
    }

    @Override
    public String toString() {
        return "Product{" +
                "part1='" + part1 + '\'' +
                ", part2='" + part2 + '\'' +
                ", part3='" + part3 + '\'' +
                '}';
    }
}

// 抽象建造者类
abstract class Builder {
    protected Product product = new Product();

    public abstract void buildPart1();
    public abstract void buildPart2();
    public abstract void buildPart3();

    public Product getResult() {
        return product;
    }
}

// 具体建造者类
class ConcreteBuilder extends Builder {
    @Override
    public void buildPart1() {
        product.setPart1("Part 1");
    }

    @Override
    public void buildPart2() {
        product.setPart2("Part 2");
    }

    @Override
    public void buildPart3() {
        product.setPart3("Part 3");
    }
}

// 导演类
class Director {
    private Builder builder;

    public Director(Builder builder) {
        this.builder = builder;
    }

    public void construct() {
        builder.buildPart1();
        builder.buildPart2();
        builder.buildPart3();
    }
}

// 客户端代码
public class BuilderPatternDemo {
    public static void main(String[] args) {
        Builder builder = new ConcreteBuilder();
        Director director = new Director(builder);
        director.construct();

        Product product = builder.getResult();
        System.out.println(product);
    }
}
```

### 代码说明：
1. **Product**：这是最终要构建的复杂对象。它包含多个部分（`part1`, `part2`, `part3`），这些部分可以通过建造者逐步设置。

2. **Builder**：这是一个抽象类或接口，定义了构建产品的各个步骤（`buildPart1`, `buildPart2`, `buildPart3`）。它还提供了一个方法（`getResult`）来返回最终构建的产品。

3. **ConcreteBuilder**：这是具体的建造者类，实现了`Builder`接口，负责实际构建产品的各个部分。

4. **Director**：导演类负责控制构建过程。它使用建造者对象来逐步构建产品。

5. **BuilderPatternDemo**：这是客户端代码，它创建了一个具体的建造者对象，并将其传递给导演类。导演类负责调用建造者的方法来构建产品，最后客户端从建造者中获取构建好的产品。

### 优点：
- **分离构建过程和表示**：建造者模式将对象的构建过程与其表示分离，使得相同的构建过程可以创建不同的表示。
- **更好的控制**：建造者模式允许你逐步构建对象，并且可以在构建过程中对对象的状态进行更精细的控制。
- **代码可读性**：当构造函数参数较多时，使用建造者模式可以使代码更加清晰和易于维护。

### 适用场景：
- 当创建复杂对象的算法应该独立于该对象的组成部分以及它们的装配方式时。
- 当构造过程必须允许被构造的对象有不同的表示时。

希望这个示例能帮助你理解建造者模式在Java中的应用！

