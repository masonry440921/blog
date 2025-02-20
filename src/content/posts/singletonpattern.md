---
title: singleton pattern
published: 2025-02-20
description: singletonpattern
tags: [design]
category: design pattern
---

单例模式（Singleton Pattern）是一种创建型设计模式，确保一个类只有一个实例，并提供一个全局访问点。在多线程环境下，单例模式的实现需要特别注意线程安全问题。以下是几种常见的线程安全的单例模式实现方式：

---

### **1. 饿汉式（Eager Initialization）**
在类加载时就创建实例，线程安全，但可能会造成资源浪费（如果实例未被使用）。

```java
public class Singleton {
    // 在类加载时创建实例
    private static final Singleton INSTANCE = new Singleton();

    // 私有构造函数，防止外部实例化
    private Singleton() {}

    // 提供全局访问点
    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

**优点**：
- 实现简单，线程安全。
- 没有加锁性能开销。

**缺点**：
- 如果实例未被使用，会造成资源浪费。

---
Chain of Responsibility Pattern
### **2. 懒汉式（Lazy Initialization）**
在第一次使用时才创建实例，需要加锁保证线程安全。

#### **2.1 简单加锁（线程安全但性能较差）**
```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {}

    // 加锁保证线程安全
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

**优点**：
- 懒加载，节省资源。

**缺点**：
- 每次调用 `getInstance` 都需要加锁，性能较差。

---

#### **2.2 双重检查锁（Double-Checked Locking）**
在加锁的基础上进行双重检查，减少加锁次数，提高性能。

```java
public class Singleton {
    // 使用 volatile 保证可见性和禁止指令重排序
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) { // 第一次检查
            synchronized (Singleton.class) { // 加锁
                if (instance == null) { // 第二次检查
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

**优点**：
- 懒加载，节省资源。
- 只有在第一次创建实例时加锁，性能较好。

**缺点**：
- 实现稍复杂，需要理解 `volatile` 和双重检查的意义。

---

### **3. 静态内部类（Static Inner Class）**
利用类加载机制保证线程安全，同时实现懒加载。

```java
public class Singleton {
    private Singleton() {}

    // 静态内部类
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

**优点**：
- 懒加载，节省资源。
- 线程安全，无需加锁。
- 实现简单。

**缺点**：
- 无法传递参数初始化实例。

---

### **4. 枚举（Enum）**
枚举实现单例是《Effective Java》推荐的方式，简洁且线程安全。

```java
public enum Singleton {
    INSTANCE;

    // 可以添加方法
    public void doSomething() {
        System.out.println("Doing something...");
    }
}
```

**优点**：
- 线程安全。
- 防止反射和序列化破坏单例。
- 实现简单。

**缺点**：
- 不够灵活（例如无法懒加载）。

---

### **总结**
| **实现方式**       | **线程安全** | **懒加载** | **性能** | **防止反射/序列化破坏** |
|--------------------|--------------|------------|----------|--------------------------|
| 饿汉式             | 是           | 否         | 高       | 否                       |
| 懒汉式（简单加锁） | 是           | 是         | 低       | 否                       |
| 双重检查锁         | 是           | 是         | 高       | 否                       |
| 静态内部类         | 是           | 是         | 高       | 否                       |
| 枚举               | 是           | 否         | 高       | 是                       |

- 如果需要懒加载且性能要求高，推荐使用 **双重检查锁** 或 **静态内部类**。
- 如果需要绝对的安全性和简洁性，推荐使用 **枚举**。


