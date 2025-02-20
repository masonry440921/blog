---
title: Observer Pattern
published: 2025-02-20
description: Observer Pattern
tags: [design]
category: design pattern
---

观察者模式（Observer Pattern）是一种行为型设计模式，它定义了对象之间的一对多依赖关系。当一个对象（被观察者）的状态发生改变时，所有依赖它的对象（观察者）都会收到通知并自动更新。

---

### **观察者模式的核心思想**
1. **被观察者（Subject）**：维护一个观察者列表，提供注册、移除和通知观察者的方法。
2. **观察者（Observer）**：定义一个更新接口，用于接收被观察者的通知。
3. **具体被观察者（Concrete Subject）**：实现被观察者的具体逻辑，并在状态改变时通知观察者。
4. **具体观察者（Concrete Observer）**：实现观察者的更新逻辑。

---

### **观察者模式的实现步骤**
1. 定义观察者接口。
2. 定义被观察者接口或抽象类。
3. 实现具体被观察者和具体观察者。
4. 客户端注册观察者并触发通知。

---

### **代码实现**

以下是一个简单的观察者模式实现示例：假设有一个天气站（被观察者），当天气数据更新时，通知多个显示设备（观察者）。

#### **1. 定义观察者接口**
```java
interface Observer {
    void update(float temperature, float humidity, float pressure);
}
```

#### **2. 定义被观察者接口**
```java
interface Subject {
    void registerObserver(Observer o); // 注册观察者
    void removeObserver(Observer o);  // 移除观察者
    void notifyObservers();            // 通知观察者
}
```

#### **3. 实现具体被观察者**
```java
import java.util.ArrayList;
import java.util.List;

class WeatherStation implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private float temperature;
    private float humidity;
    private float pressure;

    @Override
    public void registerObserver(Observer o) {
        observers.add(o);
    }

    @Override
    public void removeObserver(Observer o) {
        observers.remove(o);
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(temperature, humidity, pressure);
        }
    }

    // 当天气数据更新时调用
    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        notifyObservers(); // 通知所有观察者
    }
}
```

#### **4. 实现具体观察者**
```java
class DisplayDevice implements Observer {
    private String name;

    public DisplayDevice(String name) {
        this.name = name;
    }

    @Override
    public void update(float temperature, float humidity, float pressure) {
        System.out.println(name + " 收到更新：");
        System.out.println("温度: " + temperature + "℃, 湿度: " + humidity + "%, 气压: " + pressure + "hPa");
        System.out.println();
    }
}
```

#### **5. 客户端使用**
```java
public class Client {
    public static void main(String[] args) {
        // 创建被观察者
        WeatherStation weatherStation = new WeatherStation();

        // 创建观察者
        Observer display1 = new DisplayDevice("显示设备1");
        Observer display2 = new DisplayDevice("显示设备2");

        // 注册观察者
        weatherStation.registerObserver(display1);
        weatherStation.registerObserver(display2);

        // 更新天气数据，观察者会自动收到通知
        weatherStation.setMeasurements(25.5f, 65.0f, 1013.2f);
        weatherStation.setMeasurements(26.0f, 70.0f, 1012.8f);

        // 移除一个观察者
        weatherStation.removeObserver(display1);

        // 再次更新天气数据
        weatherStation.setMeasurements(27.5f, 68.0f, 1013.5f);
    }
}
```

---

### **输出结果**
```
显示设备1 收到更新：
温度: 25.5℃, 湿度: 65.0%, 气压: 1013.2hPa

显示设备2 收到更新：
温度: 25.5℃, 湿度: 65.0%, 气压: 1013.2hPa

显示设备1 收到更新：
温度: 26.0℃, 湿度: 70.0%, 气压: 1012.8hPa

显示设备2 收到更新：
温度: 26.0℃, 湿度: 70.0%, 气压: 1012.8hPa

显示设备2 收到更新：
温度: 27.5℃, 湿度: 68.0%, 气压: 1013.5hPa
```

---

### **观察者模式的优点**
1. **解耦**：被观察者和观察者之间是松耦合的，被观察者无需知道观察者的具体实现。
2. **动态关系**：可以在运行时动态添加或移除观察者。
3. **广播通信**：被观察者可以一次性通知多个观察者。

---

### **观察者模式的缺点**
1. **性能问题**：如果观察者过多，通知所有观察者可能会影响性能。
2. **循环依赖**：如果观察者和被观察者之间相互依赖，可能会导致循环调用。
3. **更新顺序**：观察者的更新顺序是不确定的，可能会导致意外的行为。

---

### **适用场景**
1. 当一个对象的改变需要同时改变其他对象时。
2. 当一个对象需要通知其他对象，但又不希望与被通知的对象紧耦合时。
3. 常见的应用场景包括事件处理系统、消息队列、GUI 框架中的事件监听等。

---

### **总结**
观察者模式通过定义一对多的依赖关系，实现了对象之间的松耦合通信。它的核心在于被观察者维护一个观察者列表，并在状态改变时通知所有观察者。在实际开发中，观察者模式非常适合用于事件驱动的系统或需要动态更新多个对象的场景。