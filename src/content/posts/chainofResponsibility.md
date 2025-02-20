---
title: Chain of Responsibility Pattern
published: 2025-02-20
description: Chain of Responsibility Pattern
tags: [design]
category: design pattern
---

责任链模式（Chain of Responsibility Pattern）是一种行为型设计模式，它允许多个对象有机会处理请求，从而避免请求的发送者与接收者之间的耦合。责任链模式将这些对象连成一条链，并沿着这条链传递请求，直到有对象处理它为止。

---

### **责任链模式的核心思想**
1. **抽象处理者（Handler）**：定义一个处理请求的接口，并持有下一个处理者的引用。
2. **具体处理者（Concrete Handler）**：实现处理请求的具体逻辑，如果自己不能处理，则将请求传递给下一个处理者。
3. **客户端（Client）**：创建责任链，并向链上的第一个处理者提交请求。

---

### **责任链模式的实现步骤**
1. 定义抽象处理者接口或抽象类。
2. 实现具体处理者类，每个处理者决定是否处理请求，以及是否将请求传递给下一个处理者。
3. 客户端构建责任链，并将请求发送给链上的第一个处理者。

---

### **代码实现**

以下是一个简单的责任链模式实现示例：假设有一个审批系统，不同级别的领导可以处理不同金额的报销请求。

#### **1. 定义抽象处理者**
```java
abstract class Handler {
    protected Handler nextHandler; // 下一个处理者

    // 设置下一个处理者
    public void setNextHandler(Handler nextHandler) {
        this.nextHandler = nextHandler;
    }

    // 处理请求的方法
    public abstract void handleRequest(int amount);
}
```

#### **2. 实现具体处理者**
```java
// 具体处理者：项目经理
class ProjectManager extends Handler {
    @Override
    public void handleRequest(int amount) {
        if (amount <= 1000) {
            System.out.println("项目经理审批通过，金额：" + amount);
        } else if (nextHandler != null) {
            System.out.println("项目经理无权审批，转交给下一级");
            nextHandler.handleRequest(amount); // 传递给下一个处理者
        }
    }
}

// 具体处理者：部门经理
class DepartmentManager extends Handler {
    @Override
    public void handleRequest(int amount) {
        if (amount <= 5000) {
            System.out.println("部门经理审批通过，金额：" + amount);
        } else if (nextHandler != null) {
            System.out.println("部门经理无权审批，转交给下一级");
            nextHandler.handleRequest(amount); // 传递给下一个处理者
        }
    }
}

// 具体处理者：总经理
class GeneralManager extends Handler {
    @Override
    public void handleRequest(int amount) {
        if (amount <= 10000) {
            System.out.println("总经理审批通过，金额：" + amount);
        } else {
            System.out.println("金额过大，无法审批");
        }
    }
}
```

#### **3. 客户端构建责任链并发送请求**
```java
public class Client {
    public static void main(String[] args) {
        // 创建处理者
        Handler projectManager = new ProjectManager();
        Handler departmentManager = new DepartmentManager();
        Handler generalManager = new GeneralManager();

        // 构建责任链
        projectManager.setNextHandler(departmentManager);
        departmentManager.setNextHandler(generalManager);

        // 发送请求
        projectManager.handleRequest(800);    // 项目经理审批
        projectManager.handleRequest(4000);   // 部门经理审批
        projectManager.handleRequest(9000);   // 总经理审批
        projectManager.handleRequest(20000); // 无法审批
    }
}
```

---

### **输出结果**
```
项目经理审批通过，金额：800
项目经理无权审批，转交给下一级
部门经理审批通过，金额：4000
项目经理无权审批，转交给下一级
部门经理无权审批，转交给下一级
总经理审批通过，金额：9000
项目经理无权审批，转交给下一级
部门经理无权审批，转交给下一级
金额过大，无法审批
```

---

### **责任链模式的优点**
1. **解耦**：请求的发送者和处理者之间没有直接依赖，符合单一职责原则。
2. **灵活性**：可以动态调整责任链中的处理者顺序或增减处理者。
3. **可扩展性**：新增处理者时无需修改现有代码，符合开闭原则。

---

### **责任链模式的缺点**
1. **性能问题**：如果责任链过长，可能会影响性能。
2. **请求可能未被处理**：如果没有处理者能够处理请求，请求可能会丢失。

---

### **适用场景**
1. 需要将请求的发送者和处理者解耦。
2. 需要动态指定处理请求的对象。
3. 处理流程可以分解为多个步骤，每个步骤由一个处理者负责。

---

### **总结**
责任链模式通过将多个处理者连成一条链，实现了请求的传递和处理。它的核心在于解耦和灵活性，适用于需要动态处理请求的场景。在实际开发中，可以根据需求灵活调整责任链的结构和处理逻辑。


