---
title: Java 基础——继承,接口和抽象类
date: 2020-04-10 11:30:10
tags: 
- Java
categories:
- Java
toc: true
reward: true
---
<!--more-->

## 继承

- **关键字：`extends`**

- 子类可以继承父类所有属性和方法，但不能访问父类 `private` 成员

- **单根继承原则**：每个类都只能继承一个类

  > Java 类默认继承 java.lang.Object 类

- **每个子类的构造函数的第一句话，都默认调用父类的无参数构造函数 `super()`**，除非子类的构造函数第一句话是 `super`，**而且 `super` 语句必须放在第一条**，不会出现连续两条 `super` 语句。

  例：

  ```Java
  // A 类
  public class A {
      public A() {
          System.out.println("111");
      }
  
      public A(int a) {
          System.out.println("222");
      }
  }
  
  // B 类
  public class B extends A{
      public B() {
          // super(); 如果不加，编译器默认加，输出 111
          System.out.println("333");
      }
  
      public B(int b) {
          super(10);  // 输出 222，如果不加，输出 111
          // super();  // 不能有两句 super 语句
          System.out.println("444");
      }
  
      public static void main(String[] args) {
          B obj = new B();
          B obj2 = new B(10);
      }
  }
  
  输出：
      111
      333
      222
      444
  ```

  ```java
  // A 类
  public class A {
      /* 
      public A() {
          System.out.println("111");
      }
      */
  
      public A(int a) {
          System.out.println("222");
      }
  }
  
  // B 类
  public class B extends A{
      public B() {
          // 此时编译器默认调用父类的无参构造函数，但是父类没有无参构造函数，会报错，必须显式加上 super(10)
          System.out.println("333");
      }
  
      public B(int b) {
          super(10);  
          System.out.println("444");
      }
  
      public static void main(String[] args) {
          B obj = new B(); // 报错
          B obj2 = new B(10);
      }
  }
  ```

  

- 子类可以重写父类的方法，并且**子类方法的优先级高于父类方法**

  例：

  ```Java
  // Base 类
  public class Base {
      private int num = 10;
  
      public int getNum() {
          return this.num;
      }
  }
  
  // Derived 类
  public class Derived extends Base{
      private int num = 20;
  
      public int getNum() {
          return this.num;
      }
  
      public static void main(String[] args) {
          Derived obj = new Derived();
          System.out.println(obj.getNum());
          // 输出 20 调用的是 Derived 类的 getNum()方法，此时 this 指向的是 Derived 对象
      }
  }
  
  输出：
      20
  ```

## 抽象类和接口

### 类的概念

- 属性(0 或多个) + 方法(0 或多个) 
- 一个完整(健康)的类：**所有的方法都有实现**(方法体{})
- 一个完整的类才可以**被实例化**，被 `new` 出来
- **如果一个类有方法未被实现**，则是**抽象类**

#### 抽象类

- **关键字 `abstract`**

- 一个抽象类包含成员变量，具体方法和抽象方法(需要加 `abstract` 声明)

- 一个类继承于抽象类，就不能继承于其他（抽象）类

- 如果子类继承了抽象类，则**需要实现抽象类的所有抽象方法**才是完整类，否则该子类也必须被定义为抽象类

- 抽象类不能被实例化

- 例：

  ```Java
  // Shape 抽象类
  public abstract class Shape {
      int area;
      public abstract void calArea();
  }
  
  // Rectangle 类（完整类继承抽象类）
  public class Rectangle extends Shape {
      int width;
      int length;
  
      public Rectangle(int length, int width) {
          this.width = width;
          this.length = length;
      }
  
      public void calArea() {
          System.out.println(this.length * this.width);
      }
  
      public static void main(String[] args) {
          // Shape s = new Shape(); 抽象类不能被 new
          Rectangle obj = new Rectangle(20, 10);
          obj.calArea();
      }
  }
  
  输出：
      200
  ```

### 接口

- **关键字 `interface`**

- **如果类的所有方法都没有实现，则是接口**，接口是特殊的类

- 类只能 **`extends`** 一个类，但是可以(实现) **`implements`** 多个接口，**继承和实现可以同时**

- 接口可以 **`extends`** (多个)接口，没有实现的方法将会叠加

- 类实现接口，就**必须实现所有未实现的方法**。如果没有全部实现，那么只能成为一个抽象类。

- 接口里可以定义变量，但是一般是常量

- 接口不能被实例化

- 例

  ```java
  // Animal 接口
  public interface Animal {
      public void eat();
      public void move();
  }
  
  // ClimbTree 接口
  public interface ClimbTree {
      public void climb();
  }
  
  // CatFamliy 接口（接口继承多个接口）
  public interface CatFamliy extends Animal, ClimbTree {
  
  }
  
  // Cat 类（完整类实现接口）
  public class Cat implements Animal{
      // 必须实现 Animal 接口中的所有方法，否则是抽象类
      public void eat() {
          System.out.println("Cat: I can eat.");
      }
  
      public void move() {
          System.out.println("Cat: I can move.");
      }
  }
  
  // LandAnimal 抽象类（抽象类实现接口）
  public abstract class LandAnimal implements Animal{
      public abstract void eat();
  
      public void move() {
          System.out.println("I can walk by feet.");
      }
  }
  
  // Rabbit 类（完整类继承抽象类、实现接口）
  public class Rabbit extends LandAnimal implements ClimbTree {
      public void eat() {
          System.out.println("Rabbit: I can eat.");
      }
  
      public void climb() {
          System.out.println("Rabbit: I can climb.");
      }
  }
  ```

### 抽象类和接口的异同

**相同点**：

- 两者都不能被实例化

**不同点**：

-|声明关键字|概念|继承/实现|构造函数|main 函数|权限
:-:|:-:|:-:|:-:|:-:|:-:|:-:
抽象类|abstract|部分方法实现(但至少有一个方法未实现)|extends|有|有|public/private/protected
接口|interface|所有方法不能有实现|implements|无|无|public

> 注：**接口继承接口**的关键字是**extends**，只有类实现接口才是**implements**

## 转型、多态和契约设计

### 类转型

- 类型可以相互转型，但是只限制于有继承关系的类。

  - 子类可以转换成父类（大->小），而父类不可以转为子类（小->大）。
  - 父类转为子类只有一种例外情况——**这个父类本来就是从子类转化而来**

  例：

  ```java
  // Human 类
  public class Human {
      public void eat() {
          System.out.println("I can eat.");
      }
  }
  
  // Man 类
  public class Man extends Human {
      public void eat() {
          System.out.println("I can eat more.");
      }
  
      public void plough() { };
  
      public static void main(String[] args) {
          Man obj1 = new Man();
          obj1.eat(); // call Man.eat()
  
          // 子类转型为父类后，调用普通方法，依旧是子类的方法，但无法调用子类独有的方法
          Human obj2 = (Human)obj1;
          obj2.eat(); // call Man.eat()
          // obj2 转型前是 obj1，而 obj1 是 Man 类型，所以 obj2 本质上是 Man 类型
          // obj2.plough();  obj2 没有子类独有的方法
  
          Man obj3 = (Man)obj2; // 此时允许父类转子类
          obj3.eat(); // call Man.eat()
          // obj2 本质上是 Man，所以 obj3 也是 Man
          obj3.plough(); // obj3 其实和 obj1 等价
          // obj1, obj2, obj3 指向的其实是同一块内存
  
          Human obj4 = new Man();  // 子类->父类 可以
          // Man obj5 = new Human();  // 父类->子类 不可以
  
          Human obj6 = new Man();
          Man obj7 = (Man)obj6;  // 父类->子类 可以
          // 前提：父类是由子类转换而来的
          // 如果像下面↓ 就会报错
          // Human obj8 = new Human();
          // Man obj9 = (Man)obj8;
      }
  }
  
  输出：
      I can eat more.
      I can eat more.
      I can eat more.
  ```

### 多态

- 类型转换，带来的作用就是多态
- 子类继承父类的所有方法，但子类可以重新定义一个名字、参数和父类一样的方法，这种行为就是**重写（不是重载）**
- 子类的方法的优先级高于父类的

**作用**：

- 以统一的接口来操纵某一类中不同的对象的动态行为
- 对象之间的解耦 

### 契约设计

- **契约**：规定规范了对象应该包含的行为方法
- **契约设计**：类不会直接使用另外一个类，而是采用**接口**的形式，外部可以“空投”**这个接口下的任意子类对象**（如下方例子中的 `haveLunch` 方法）
- 接口定义了方法的名称、参数和返回值，规范了派生类的行为
- 基于接口，利用**转型**和**多态**，不影响真正方法的调用，成功地将调用类和被调用类**解耦(decoupling)**

转型、多态、契约设计、**匿名类**的例子：

```java
// Animal 接口
public interface Animal {
    public void eat();
    public void move();
}

// Cat 类
public class Cat implements Animal {
    public void eat() {
        System.out.println("Cat: I can eat.");
    }

    public void move() {
        System.out.println("Cat: I can move.");
    }
}

// Dog 类
public class Dog implements Animal {
    public void eat() {
        System.out.println("Dog: I can eat.");
    }

    public void move() {
        System.out.println("Dog: I can move.");
    }
}

// 测试类，main 函数
public class AnimalTest {
    public static void haveLunch(Animal a) {
        a.eat();  // 此处的 Animal 是接口，而传进来的 a 的类型可能是其它的具体的继承了该接口的类，以此来实现多态
    }

    public static void main(String[] args) {
        Animal[] as = new Animal[4];
        // 注意，接口本身不能 new，但是可以定义接口数组
        // 但是 Animal a = new Animal() 是不可以的
        as[0] = new Cat(); // 隐藏了 Cat->Animal 操作 Animal as[0] = new Cat()
        as[1] = new Dog(); // 隐藏了 Dog->Animal 操作
        as[2] = new Cat();
        as[3] = new Dog();

        // Cat[] as1 = new Cat[4];
        // as1[0] = new Animal();  // 父类->子类错误

        for (int i = 0; i < as.length; i++) {
            as[i].move(); // 调用每个元素自身的 move 方法
            // 即用统一的接口来操纵某一类中不同对象的动态行为
        }

        for (int i = 0; i < as.length; i++) {
            haveLunch(as[i]); // 调用每个元素自身的 eat 方法
        }

        haveLunch(new Cat()); // 隐藏操作：Animal a = new Cat()
        haveLunch(new Dog());

        // 匿名内部类的匿名对象
        haveLunch(
                // new Animal()本来是不行的
                // 必须把其中的所有方法都补全才可以 new
                // 匿名类（相当于 Animal 的子类）只能用一次
                new Animal() {
                    public void eat() {
                        System.out.println("I can eat from an anonymous class.");
                    }

                    public void move() {
                        System.out.println("I can eat from anonymous class.");
                    }
                }
        );
    }
}

输出：
    Cat: I can move.
    Dog: I can move.
    Cat: I can move.
    Dog: I can move.
    Cat: I can eat.
    Dog: I can eat.
    Cat: I can eat.
    Dog: I can eat.
    Cat: I can eat.
    Dog: I can eat.
    I can eat from an anonymous class.
```