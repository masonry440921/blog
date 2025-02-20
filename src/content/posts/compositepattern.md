---
title: Composite Pattern
published: 2025-02-20
description: ""
tags: [design]
category: design pattern
---

组合模式（Composite Pattern）是一种结构型设计模式，它允许你将对象组合成树形结构以表示“部分-整体”的层次结构。组合模式使得客户端可以统一对待单个对象和组合对象。

---

### **组合模式的核心思想**
1. **组件接口（Component）**：定义叶子节点和组合节点的共同接口。
2. **叶子节点（Leaf）**：表示树形结构中的叶子对象，没有子节点。
3. **组合节点（Composite）**：表示树形结构中的组合对象，可以包含子节点（叶子节点或其他组合节点）。
4. **客户端（Client）**：通过组件接口操作叶子节点和组合节点。

---

### **组合模式的实现步骤**
1. 定义组件接口，声明叶子节点和组合节点的共同操作。
2. 实现叶子节点类，表示树形结构中的叶子对象。
3. 实现组合节点类，表示树形结构中的组合对象，并管理子节点。
4. 客户端通过组件接口操作叶子节点和组合节点。

---

### **代码实现**

以下是一个简单的组合模式实现示例：假设有一个文件系统的树形结构，其中文件夹（组合节点）可以包含文件（叶子节点）或其他文件夹。

#### **1. 定义组件接口**
```java
interface FileSystemComponent {
    void showDetails(); // 显示详细信息
}
```

#### **2. 实现叶子节点（文件）**
```java
class File implements FileSystemComponent {
    private String name;

    public File(String name) {
        this.name = name;
    }

    @Override
    public void showDetails() {
        System.out.println("文件: " + name);
    }
}
```

#### **3. 实现组合节点（文件夹）**
```java
import java.util.ArrayList;
import java.util.List;

class Folder implements FileSystemComponent {
    private String name;
    private List<FileSystemComponent> components = new ArrayList<>();

    public Folder(String name) {
        this.name = name;
    }

    // 添加子节点
    public void addComponent(FileSystemComponent component) {
        components.add(component);
    }

    // 移除子节点
    public void removeComponent(FileSystemComponent component) {
        components.remove(component);
    }

    @Override
    public void showDetails() {
        System.out.println("文件夹: " + name);
        for (FileSystemComponent component : components) {
            component.showDetails(); // 递归显示子节点的详细信息
        }
    }
}
```

#### **4. 客户端使用**
```java
public class Client {
    public static void main(String[] args) {
        // 创建叶子节点（文件）
        FileSystemComponent file1 = new File("文件1.txt");
        FileSystemComponent file2 = new File("文件2.txt");
        FileSystemComponent file3 = new File("文件3.txt");

        // 创建组合节点（文件夹）
        Folder folder1 = new Folder("文件夹1");
        Folder folder2 = new Folder("文件夹2");

        // 构建树形结构
        folder1.addComponent(file1);
        folder1.addComponent(file2);
        folder2.addComponent(file3);
        folder1.addComponent(folder2);

        // 显示详细信息
        folder1.showDetails();
    }
}
```

---

### **输出结果**
```
文件夹: 文件夹1
文件: 文件1.txt
文件: 文件2.txt
文件夹: 文件夹2
文件: 文件3.txt
```

---

### **组合模式的优点**
1. **统一性**：客户端可以统一对待叶子节点和组合节点，简化了客户端代码。
2. **灵活性**：可以动态地添加或删除节点，扩展性强。
3. **层次结构**：非常适合表示树形结构或部分-整体的层次关系。

---

### **组合模式的缺点**
1. **复杂性增加**：如果树形结构过于复杂，可能会导致设计变得难以理解。
2. **类型检查**：客户端可能需要检查节点的类型（叶子节点或组合节点），这可能会破坏统一性。

---

### **适用场景**
1. 当需要表示对象的“部分-整体”层次结构时。
2. 当希望客户端可以忽略组合对象与单个对象的差异时。
3. 常见的应用场景包括文件系统、菜单系统、组织架构等。

---

### **总结**
组合模式通过将对象组织成树形结构，使得客户端可以统一对待单个对象和组合对象。它的核心在于组件接口、叶子节点和组合节点的设计。在实际开发中，组合模式非常适合用于表示层次结构或部分-整体的关系。

