---
title: Java 基础——static, 单例模式, final
date: 2020-04-24 12:38:10
tags: 
- Java
categories:
- Java
toc: true
reward: true
---

<!--more-->

## `static` 关键字

`static` 关键字可作用在：

- 变量
- 方法
- 类
- 匿名方法块

### 静态变量

类共有成员，只依赖于类存在（**通过类即可访问，不需要通过对象实例**），所有对象实例中的 `static` 变量都**共享**存储在栈中。

> 例如一个类中定义了一个 `price` 的静态变量，那么无论这个类有多少个实例，在内存中都只有一个 `price` 变量（被每个实例共享）。

```java
public class Potato {
    static int price = 5;
    String content = "";

    public Potato(int price, String content) {
        this.price = price;
        this.content = content;
    }

    public static void main(String[] args) {
        System.out.println(Potato.price); // Potato.content => error
        System.out.println("---------------------------");

        Potato obj1 = new Potato(10, "青椒土豆丝");
        System.out.println(obj1.price);
        System.out.println(Potato.price);
        System.out.println("---------------------------");

        Potato obj2 = new Potato(20, "鱼香肉丝");
        System.out.println(obj2.price);
        System.out.println(obj1.price);
        System.out.println(Potato.price);
    }
}

输出：
    5
    ---------------------------
    10
    10
    ---------------------------
    20
    20
    20
```

### 静态方法

- 静态方法也无需通过对象来引用，而通过类名可以直接引用
- **在静态方法中，只能使用静态变量，不能使用非静态变量**
- **静态方法禁止引用非静态方法**

> 非静态方法（普通方法）既可以使用静态变量，也可以调用静态方法

```java
public class StaticMethodTest {
    int a = 111;
    static int b = 222;

    public static void hello() {
        // System.out.println(a);  // error: cannot call non-static variables
        System.out.println(b);  // ok: can call static variables
        // hi();  // error: cannot call non-static functions
    }

    public void hi() {
        System.out.println(a);  // ok: can call non-static variables
        System.out.println(b);  // ok: can call static variables
        hello();  // ok: can call non-static functions
    }

    public static void main(String[] args) {
        StaticMethodTest.hello();
        // StaticMethodTest.hi();  // error

        StaticMethodTest obj = new StaticMethodTest();
        obj.hello();  // warning: but it is ok, better not to use static function in an object
        obj.hi();
    }
}

输出：
    222
    222
    111
    222
    222
```



### 静态类（内部类）

关于 `static` 修饰类(内部类)，使用的机会较少，暂忽略不计。

### `static` 块

- **只在类第一次被加载时调用**。 
- 换句话说，**在整个程序运行期间**，这段代码只运行一次。 
- 执行顺序：`static` 块 > 匿名块（每次 `new` 都会运行） > 构造函数
- 不建议使用 `static` 块代码或匿名块代码（可读性、易理解性差）

```java
public class StaticBlock {
    // static block > anonymous block > constructor function

    static {
        System.out.println("111111");
    }

    public StaticBlock() {
        System.out.println("222222");
    }

    {
        System.out.println("333333");
    }
}

public class StaticBlockTest {
    public static void main(String[] args) {
        System.out.println("000000");
        // static block used only once
        StaticBlock obj1 = new StaticBlock();
        System.out.println("---------------");
        StaticBlock obj2 = new StaticBlock();
    }
}

输出：
    000000
    111111
    333333
    222222
    ---------------
    333333
    222222
```

## 单例模式

- 单例模式，又名单态模式，$Singleton$
- 限定某一个类在整个程序运行过程中，**只能保留一个实例对象在内存空间**。
- 单例模式是 $GoF$ 的 23 种设计模式($Design Pattern$)中经典的一种，属于创建型模式类型。

**设计模式**：在软件开发过程中，经过验证的，用于解决在特定环境下的、重复出现的、特定问题的解决方案。 

> 设计模式起源于建筑领域。$Alexander$ 总结了建筑行业的设计模式。
>
> 1995 年 $Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides(GoF)$合著的《设计模式—可复用面向对象软件基础》总结了常见的 23 种设计模式，包括：创建型、结构型和行为型。

**单例模式**：**保证一个类有且只有一个对象**

- 采用 `static` 来共享对象实例
- 采用 `private` 构造函数，防止外界 `new` 操作

```java
public class SingleTon {
    private static SingleTon obj = new SingleTon();  // 共享同一个对象
    private String content;

    private SingleTon() {  // private 确保只能在类内部调用构造函数
        this.content = "abc";
    }

    public void setContent(String content) {
        this.content = content;
    }

    public String getContent() {
        return this.content;
    }

    public static SingleTon getObj() {
        // 静态方法可以使用静态变量
        // 另外可以使用方法内的临时变量，但是不能引用非静态的成员变量
        return obj;
        // return this.obj;  // error
    }

    public static void main(String[] args) {
        SingleTon obj1 = SingleTon.getObj();
        SingleTon obj2 = SingleTon.getObj();

        System.out.println(obj1.getContent());
        System.out.println(obj2.getContent());
        System.out.println("---------------");

        obj1.setContent("def");
        System.out.println(obj1.getContent());
        System.out.println(obj2.getContent());
        System.out.println("---------------");

        System.out.println(obj1 == obj2); // true
        // 实际上不管是 obj1 还是 obj2，都是类内部的 obj 这一指针
    }
}

输出：
    abc
    abc
    ---------------
    def
    def
    ---------------
    true
    
// 在外部类中测试
public class SingleTonTest {
    // SingleTon a = new SingleTon(); // error
    SingleTon b = SingleTon.getObj(); // 本质上还是类内部的 obj
}
```

## `final` 关键字

`final` 关键字可作用在：

- 类
- 方法
- 字段

### `final` 类

**`final` 的类，不能被继承**：

```java
public final class FinalFather {
}

// error
public class son1 extends FinalFather {
}
```

### `final` 方法

**父类中如果有 `final` 的方法，子类中不能改写 `override`（不是重载 `overload`）此方法**：

```java
// FinalMethodFather 类
public class FinalMethodFather {
    public final void f1() {

    }
}

// FinalMethodSon 类
public class FinalMethodSon extends FinalMethodFather{
    // error: cannot override the final method from FinalMethodFather
    public void f1() {

    }
}
```

### `final` 变量

**`final` 的变量，不能再次赋值**

如果是基本型别的变量，不能修改其值：

```java
public class FinalPrimitiveType {
    public static void main(String[] args) {
        final int a = 5;
        // error: cannot assign a value to a final variable
        a = 10;
    }
}
```

如果是对象实例，那么不能修改其指针（**但是可以修改对象内部的值**）：

```java
class FinalObject {
    int a = 10;
}

public class FinalObjectTest {
    public static void main(String[] args) {
        final FinalObject obj = new FinalObject();
        System.out.println(obj.a);
        obj.a = 5;  // ok: can assign a value to a variable in the final object
        System.out.println(obj.a);
        
        // obj = new FinalObject();  // error: cannot change the pointer of the final object
    }
}

输出：
    10
    5
```

`final` 总结：

- `final` 类：没有子类继承 
- `final` 方法：不能被子类改写 
- `final` 变量：基本类型不能修改值，对象类型不能修改指针