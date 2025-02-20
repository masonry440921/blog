---
title: protoype pattern
published: 2025-01-05
description: ""
tags: [design]
category: design pattern
---

原型模式（Prototype Pattern）是一种创建型设计模式，它通过复制现有对象来创建新对象，而不是通过调用构造函数。原型模式的核心思想是通过克隆（Clone）来避免重复创建对象的开销，特别适用于创建成本较高的对象。

---

### **原型模式的核心思想**
1. **原型接口（Prototype）**：定义一个克隆自身的接口。
2. **具体原型类（Concrete Prototype）**：实现原型接口，提供克隆方法。
3. **客户端（Client）**：通过调用原型对象的克隆方法来创建新对象。

---

### **原型模式的实现步骤**
1. 定义原型接口或抽象类，声明克隆方法。
2. 实现具体原型类，实现克隆方法。
3. 客户端通过克隆方法创建新对象。

---

### **代码实现**

以下是一个简单的原型模式实现示例：假设有一个图形类，可以通过克隆来创建新的图形对象。

#### **1. 定义原型接口**
```java
interface Prototype {
    Prototype clone(); // 克隆方法
}
```

#### **2. 实现具体原型类**
```java
class Circle implements Prototype {
    private String color;

    public Circle(String color) {
        this.color = color;
    }

    // 实现克隆方法
    @Override
    public Prototype clone() {
        return new Circle(this.color); // 创建一个新对象并复制属性
    }

    @Override
    public String toString() {
        return "Circle{color='" + color + "'}";
    }
}

class Rectangle implements Prototype {
    private int width;
    private int height;

    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    // 实现克隆方法
    @Override
    public Prototype clone() {
        return new Rectangle(this.width, this.height); // 创建一个新对象并复制属性
    }

    @Override
    public String toString() {
        return "Rectangle{width=" + width + ", height=" + height + "}";
    }
}
```

#### **3. 客户端使用**
```java
public class Client {
    public static void main(String[] args) {
        // 创建原型对象
        Prototype circlePrototype = new Circle("Red");
        Prototype rectanglePrototype = new Rectangle(10, 20);

        // 通过克隆创建新对象
        Prototype newCircle = circlePrototype.clone();
        Prototype newRectangle = rectanglePrototype.clone();

        // 输出新对象
        System.out.println("Original Circle: " + circlePrototype);
        System.out.println("Cloned Circle: " + newCircle);

        System.out.println("Original Rectangle: " + rectanglePrototype);
        System.out.println("Cloned Rectangle: " + newRectangle);
    }
}
```

---

### **输出结果**
```
Original Circle: Circle{color='Red'}
Cloned Circle: Circle{color='Red'}
Original Rectangle: Rectangle{width=10, height=20}
Cloned Rectangle: Rectangle{width=10, height=20}
```

---

### **原型模式的优点**
1. **性能优化**：通过克隆避免重复创建对象的开销，特别适用于创建成本较高的对象。
2. **简化对象创建**：客户端无需知道具体类的构造细节，只需调用克隆方法。
3. **动态扩展**：可以在运行时动态添加或修改原型对象。

---

### **原型模式的缺点**
1. **深拷贝与浅拷贝问题**：如果对象包含引用类型的属性，需要实现深拷贝，否则克隆对象和原对象会共享引用属性。
2. **复杂性增加**：如果对象的属性较多或结构复杂，实现克隆方法可能会变得复杂。

---

### **深拷贝与浅拷贝**
- **浅拷贝**：只复制对象的基本类型属性，引用类型的属性仍然共享。
- **深拷贝**：复制对象的所有属性，包括引用类型的属性。

#### **深拷贝实现示例**
```java
class DeepCopyExample implements Prototype {
    private int value;
    private List<String> list;

    public DeepCopyExample(int value, List<String> list) {
        this.value = value;
        this.list = new ArrayList<>(list); // 创建新的列表
    }

    @Override
    public Prototype clone() {
        // 深拷贝：复制所有属性
        return new DeepCopyExample(this.value, new ArrayList<>(this.list));
    }

    @Override
    public String toString() {
        return "DeepCopyExample{value=" + value + ", list=" + list + "}";
    }
}
```

---

### **适用场景**
1. 当创建对象的成本较高（如需要复杂的初始化操作）时。
2. 当需要动态创建对象，且对象的结构与现有对象相似时。
3. 当需要避免使用构造函数创建对象时。

---

### **总结**
原型模式通过克隆现有对象来创建新对象，避免了重复创建对象的开销。它的核心在于实现克隆方法，并注意深拷贝与浅拷贝的问题。在实际开发中，原型模式非常适合用于创建成本较高的对象或需要动态扩展的场景。

