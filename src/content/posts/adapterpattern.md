---
title: Adapter pattern
published: 2025-01-05
description: ""
tags: [design]
category: design pattern
---

适配器模式（Adapter Pattern）是一种结构型设计模式，它允许不兼容的接口之间进行协作。适配器模式通过将一个类的接口转换成客户端期望的另一个接口，使得原本由于接口不兼容而无法一起工作的类可以一起工作。

---

### **适配器模式的核心思想**
1. **目标接口（Target）**：客户端期望的接口。
2. **被适配者（Adaptee）**：需要被适配的现有类。
3. **适配器（Adapter）**：实现目标接口，并持有被适配者的引用，通过调用被适配者的方法来实现目标接口的功能。

---

### **适配器模式的实现方式**
适配器模式有两种常见的实现方式：
1. **类适配器**：通过继承被适配者来实现适配器。
2. **对象适配器**：通过组合（持有被适配者的引用）来实现适配器。

---

### **代码实现**

以下是一个简单的适配器模式实现示例：假设有一个旧的 `LegacyPrinter` 类，它的接口与客户端期望的 `Printer` 接口不兼容，需要通过适配器来适配。

#### **1. 目标接口（Target）**
```java
interface Printer {
    void print(String text);
}
```

#### **2. 被适配者（Adaptee）**
```java
class LegacyPrinter {
    void printDocument(String text) {
        System.out.println("Legacy Printer: " + text);
    }
}
```

#### **3. 适配器（Adapter）**
##### **3.1 类适配器（通过继承实现）**
```java
class ClassAdapter extends LegacyPrinter implements Printer {
    @Override
    public void print(String text) {
        printDocument(text); // 调用被适配者的方法
    }
}
```

##### **3.2 对象适配器（通过组合实现）**
```java
class ObjectAdapter implements Printer {
    private LegacyPrinter legacyPrinter;

    public ObjectAdapter(LegacyPrinter legacyPrinter) {
        this.legacyPrinter = legacyPrinter;
    }

    @Override
    public void print(String text) {
        legacyPrinter.printDocument(text); // 调用被适配者的方法
    }
}
```

#### **4. 客户端使用**
```java
public class Client {
    public static void main(String[] args) {
        // 使用类适配器
        Printer classAdapter = new ClassAdapter();
        classAdapter.print("Hello, Class Adapter!");

        // 使用对象适配器
        LegacyPrinter legacyPrinter = new LegacyPrinter();
        Printer objectAdapter = new ObjectAdapter(legacyPrinter);
        objectAdapter.print("Hello, Object Adapter!");
    }
}
```

---

### **输出结果**
```
Legacy Printer: Hello, Class Adapter!
Legacy Printer: Hello, Object Adapter!
```

---

### **适配器模式的优点**
1. **兼容性**：可以让不兼容的接口一起工作。
2. **复用性**：可以复用现有的类，而无需修改其代码。
3. **灵活性**：可以选择类适配器或对象适配器，根据需求灵活实现。

---

### **适配器模式的缺点**
1. **复杂性增加**：如果系统中适配器过多，可能会增加代码的复杂性。
2. **性能开销**：适配器模式可能会引入额外的调用层次，导致性能略有下降。

---

### **适配器模式 vs 装饰器模式**
- **适配器模式**：用于解决接口不兼容的问题，重点在于转换接口。
- **装饰器模式**：用于动态扩展对象的功能，重点在于增强功能。

---

### **适用场景**
1. 当需要使用现有的类，但其接口与系统要求的接口不兼容时。
2. 当需要创建一个可以复用的类，用于与多个不相关的类协作时。
3. 当需要统一多个类的接口时。

---

### **总结**
适配器模式通过将一个类的接口转换成客户端期望的另一个接口，解决了接口不兼容的问题。它的核心在于适配器类，适配器类可以通过继承（类适配器）或组合（对象适配器）来实现。在实际开发中，适配器模式非常适合用于集成第三方库或遗留代码。

