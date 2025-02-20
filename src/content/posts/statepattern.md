---
title: Strategy and State
published: 2025-02-20
description: strategy
tags: [design]
category: design pattern
---
## 策略模式（Strategy Pattern）和状态模式（State Pattern）是两种行为型设计模式，它们的核心思想都是通过将行为或状态抽象出来，实现灵活的变化和扩展。然而，它们的应用场景和目的有所不同。

---

### **1. 策略模式（Strategy Pattern）**
**目的**：定义一系列算法或行为，使它们可以互相替换，让算法的变化独立于使用它的客户端。

**核心思想**：
- 将算法或行为抽象为一个接口或抽象类。
- 每个具体的算法或行为实现该接口。
- 客户端持有一个策略接口的引用，并可以在运行时动态切换策略。

**适用场景**：
- 当一个系统需要在多种算法或行为之间动态切换时。
- 当需要避免使用大量的条件语句（如 `if-else` 或 `switch-case`）来选择不同的行为时。

**优点**：
- 算法可以独立于客户端变化。
- 避免了代码重复和复杂的条件判断。
- 易于扩展新的策略。

**缺点**：
- 客户端需要了解不同的策略，增加了使用复杂度。
- 如果策略过多，可能会导致类的数量增加。

**示例**：
```java
// 策略接口
interface PaymentStrategy {
    void pay(int amount);
}

// 具体策略
class CreditCardPayment implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " via Credit Card.");
    }
}

class PayPalPayment implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " via PayPal.");
    }
}

// 上下文（客户端）
class ShoppingCart {
    private PaymentStrategy paymentStrategy;

    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;
    }

    public void checkout(int amount) {
        paymentStrategy.pay(amount);
    }
}

// 使用
public class Main {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();
        cart.setPaymentStrategy(new CreditCardPayment());
        cart.checkout(100);

        cart.setPaymentStrategy(new PayPalPayment());
        cart.checkout(200);
    }
}
```

---

### **2. 状态模式（State Pattern）**
**目的**：允许对象在其内部状态改变时改变它的行为，使对象看起来像是修改了它的类。

**核心思想**：
- 将对象的状态抽象为一个接口或抽象类。
- 每个具体状态实现该接口，并定义在该状态下的行为。
- 上下文对象持有一个状态对象的引用，并将行为委托给当前状态对象。

**适用场景**：
- 当一个对象的行为取决于它的状态，并且需要在运行时根据状态改变行为时。
- 当代码中有大量与对象状态相关的条件语句时。

**优点**：
- 将状态相关的行为局部化到具体的状态类中，避免了复杂的条件判断。
- 使状态转换更加明确和可维护。
- 易于扩展新的状态。

**缺点**：
- 如果状态过多，可能会导致类的数量增加。
- 状态转换逻辑可能分散在上下文和状态类中，增加理解难度。

**示例**：
```java
// 状态接口
interface State {
    void handle(Context context);
}

// 具体状态
class StateA implements State {
    public void handle(Context context) {
        System.out.println("Handling in State A.");
        context.setState(new StateB()); // 切换到下一个状态
    }
}

class StateB implements State {
    public void handle(Context context) {
        System.out.println("Handling in State B.");
        context.setState(new StateA()); // 切换回上一个状态
    }
}

// 上下文
class Context {
    private State currentState;

    public Context(State state) {
        this.currentState = state;
    }

    public void setState(State state) {
        this.currentState = state;
    }

    public void request() {
        currentState.handle(this);
    }
}

// 使用
public class Main {
    public static void main(String[] args) {
        Context context = new Context(new StateA());
        context.request(); // 输出: Handling in State A.
        context.request(); // 输出: Handling in State B.
        context.request(); // 输出: Handling in State A.
    }
}
```

---

### **策略模式 vs 状态模式**

| **特性**            | **策略模式**                          | **状态模式**                          |
|---------------------|---------------------------------------|---------------------------------------|
| **目的**            | 动态切换算法或行为                    | 根据对象的状态改变行为                |
| **核心**            | 客户端主动选择策略                    | 状态之间的转换由状态类或上下文控制    |
| **关注点**          | 算法的替换                            | 状态的变化及其对行为的影响            |
| **客户端参与**      | 客户端需要显式设置策略                | 客户端通常不直接参与状态转换          |
| **状态转换**        | 无状态转换的概念                      | 状态之间可以相互转换                  |
| **适用场景**        | 需要灵活切换算法                      | 对象行为依赖于状态，且状态需要频繁切换|

---

### **总结**
- **策略模式**：关注的是算法的灵活替换，客户端主动选择策略。
- **状态模式**：关注的是对象状态的变化及其对行为的影响，状态转换通常由状态类或上下文控制。

选择哪种模式取决于你的需求：如果需要动态切换行为，使用策略模式；如果行为依赖于对象的状态，使用状态模式。



