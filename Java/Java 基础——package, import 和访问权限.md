---
title: Java 基础——package, import 和访问权限
date: 2020-05-08 18:34:10
tags: 
- Java
categories:
- Java
toc: true
reward: true
---

<!--more-->

## `package`

- 所有的 `Java` 类都是放置在同一个目录下面的，因此类之间的相互调用无需显式声明调用。 
  - 同一个目录下，两个类的名字不能相同
  - 文件过多，查找和修改都不易，且容易出错
-  `Java` 支持多个目录放置 `Java`，并且通过 `package/import/classpath/jar` 等机制配合使用，可以支持跨目录放置和调用 `Java` 类。

例：

```java
// cn/edu/cpu/PackageExample.java

package cn.edu.cpu;
public class PackageExample {
    
}
```

- package 包，和 C++中 `namespace` 类似
- 在 Java 类文件的第一句话给出包的名称
- 类全称 `cn.edu.cpu.PackageExample` ，短名称 `PackageExample`
  - 类全称（长名称）= 包名+类名
  - 短名称=类名
- **引用类的时候，必须采用全称引用；程序正文可以用短名称**
- `PackageExample.java` 必须严格放置在 `cn/edu/cpu` 目录下



- 包名 `package name` 尽量唯一
- 域名是唯一的，因此常用域名做包名
- 域名逆序：`cn.edu.cpu`，范围通常**从大到小**
- 类的完整名字：包名+类名，`cn.edu.cpu.PackageExample`
- 包名：和目录层次一样, `cn\edu\cpu\PackageExample.java`
- 但是包(`cn`)具体放在什么位置不重要，编译和运行的时候再指定。

## `import`

例：

```java
package cn.edu.cpu;
import cn.edu.cpu.PackageExample;
// 也可以采用 import cn.edu.cpu.*
// 但是不能用 import cn.*
// 因为*号只是代表此目录下的所有文件，不包括子文件夹和其中的文件
// 如果 PackageExample 和当前类在一个目录，可以上句省略 import

public class PackageExampleTest {
    public static void main(String[] args) {
        PackageExample obj = new PackageExample();
        // 此处可以用类的短名称来引用
    }
}
```

- `import` 规则
  - **`import` 必须全部放在 `package` 之后，类定义之前。**
  - 多个 `import` 的顺序无关。
  - 可以用 `*` 来引入一个目录下的所有类，比如 `import java.lang.*;` 此意思是引入 `java.lang` 下面所有的类文件，但**不包括 `java.lang` 下面所有的子目录文件**，即并不包括 `java.lang.reflect.*;`  换句话说，不能递归包含各个目录下的文件。
  - `import` 尽量精确，不推荐用 `*`，以免新增的同名程序会使得老程序报错。

例一：

```java
// MOOC0701/com/test/NewExample.java
package MOOC0701.com.test;

public class NewExample {
    public void hello() {
        System.out.println("hello");
    }
}

// MOOC0701/net/abc/NewExampleTest.java
package MOOC0701.net.abc;
import MOOC0701.com.test.NewExample;

public class NewExampleTest {
    public static void main(String[] args) {
        new NewExample().hello();
    }
}

输出：hello
```

例二：同名类造成的麻烦

```java
// MOOC0701/a/Man.java
package MOOC0701.a;

public class Man {
}

// MOOC0701/b/Man.java
package MOOC0701.b;

public class Man {
}

// MOOC0701/c/Test.java
package MOOC0701.c;
import MOOC0701.a.*;
import MOOC0701.b.*;

public class Test {
    public static void main(String[] args) {
        //Man obj = new Man(); // error
        MOOC0701.a.Man obj = new MOOC0701.a.Man();
    }
}

// MOOC0701/d/Test.java
package MOOC0701.d;
import MOOC0701.a.Man;
// import MOOC0701.b.Man; // error

public class Test {
    public static void main(String[] args) {
        Man obj = new Man();
        MOOC0701.b.Man obj2 = new MOOC0701.b.Man(); // 没有 import 必须使用全名
    }
}
```

> 程序中需要引用多个同名的类，那么只能 `import` 其中一个，并且不能用 `*` ，`import` 的类可以直接用类名调用，其他的类必须用全称（**包名+类名**）调用。

## `package` 和 `import` 总结

- `Java` 通过包(`package`)来**区分**（同名）类
  - `package` 必须和目录层次一样
- `Java` 通过引用(`import`)来**导入**类
  - `import` 类尽量准确

## `jar` 文件导出和导入

- `jar` 文件，一种扩展名为 `jar` 的文件，是 `Java` 所**特有**的一种文件格式，用于可执行程序文件的传播。
- `jar` 文件实际上是一组 `class` 文件的**压缩包**

**`jar` 文件优势**

- `jar` 文件可以包括多个 `class`，比多层目录更加简洁实用
- `jar` 文件经过压缩，只有一个文件，在网络下载和传播方面，更具有优势
- `jar` 文件只包括 `class`，而没有包含 `java` 文件，在保护源文件知识版权方面，能够可以起到更好的作用
- 将多个 `class` 文件压缩成 `jar` 文件（只有一个文件），可以规定给一个版本号，更容易进行版本控制

## `Java` 访问权限

- `Java` 访问权限有四种
  - `private`：私有的，只能本类访问
  - `default`(通常忽略不写)：同一个包内访问
  - `protected`：同一个包，或子类均可以访问
  - `public`：公开的，所有类都可以访问
- 使用范围
  - 四种都可以用来修饰**成员变量、成员方法、构造函数**
  - **`default` 和 `public` 可以修饰类**

&nbsp;|同一个类|同一个包|不同包的子类|不同包的非子类
:-:|:-:|:-:|:-:|:-:
private|√| | | 
default|√|√| | 
protected|√|√|√| 
public|√|√|√|√

例：（代码块中注释的都是错误的语句）

```java
// MOOC0704/test1/A.java
package MOOC0704.test1;
public class A {
    private int v1 = 1;
    int v2 = 2;  // default int v2 = 2; 会报错，default 不能写
    protected int v3 = 3;
    public int v4 = 4;

    private void showV1() {
        System.out.println(v1);
    }
    void showV2() {
        System.out.println(v2);
    }
    protected void showV3() {
        System.out.println(v3);
    }
    public void showV4() {
        System.out.println(v4);
    }
}

// MOOC0704/test1/B.java
package MOOC0704.test1;
public class B {
    public void show() {
        // B is not subclass of A
        // System.out.println(v1);
        // System.out.println(v2);
        // System.out.println(v3);
        // System.out.println(v4);
        // showV1();
        // showV2();
        // showV3();
        // showV4();

        A obj = new A();
        // System.out.println(obj.v1);  //在同一个包内，不能访问 private
        System.out.println(obj.v2);
        System.out.println(obj.v3);
        System.out.println(obj.v4);

        // obj.showV1();
        obj.showV2();
        obj.showV3();
        obj.showV4();
    }
}

// MOOC0704/test1/C.java
package MOOC0704.test1;
// C is a subclass of A, and in the same package of A.
public class C extends A {
    public void show() {
        // 以下代码：C 作为 A 的子类访问 A 的成员
        // 子类可以直接访问父类非 private 成员
        // System.out.println(v1);
        System.out.println(v2);
        System.out.println(v3);
        System.out.println(v4);
        // showV1();
        showV2();
        showV3();
        showV4();

        // 以下代码：C 作为与 A 同包平等的身份访问 A 的成员
        A obj = new A();
        // System.out.println(v1);
        System.out.println(obj.v2);
        System.out.println(obj.v3);
        System.out.println(obj.v4);
        // obj.showV1();
        obj.showV2();
        obj.showV3();
        obj.showV4();
    }
}

// MOOC0704/test2/D.java
package MOOC0704.test2;
import MOOC0704.test1.A;
public class D extends A{
    public void show() {
        // 以下代码：D 作为 A 的子类访问 A 的成员
        // 不同包的子类可以直接访问父类非 private 和非 default 成员
        // System.out.println(v1);
        // System.out.println(v2);  default
        System.out.println(v3);
        System.out.println(v4);
        // showV1();
        // showV2();  default
        showV3();
        showV4();

        // 以下代码：D 作为与 A 同包平等的身份访问 A 的成员
        A obj = new A();
        // System.out.println(obj.v1);
        // System.out.println(obj.v2);
        // System.out.println(obj.v3);  注意此时是和 A 平等的身份，由于不在同一个包，因此 protected 成员也不能访问
        System.out.println(obj.v4);
        // obj.showV1();
        // obj.showV2();
        // obj.showV3();
        obj.showV4();
    }
}

// MOOC0704/test2/E.java
package MOOC0704.test2;
import MOOC0704.test1.A;
public class E {
    public void show() {
        // 以下代码：D 作为 A 的子类访问 A 的成员
        // 不同包的子类可以直接访问父类非 private 和非 default 成员
        // System.out.println(v1);
        // System.out.println(v2);  default
        // System.out.println(v3);
        // System.out.println(v4);
        // showV1();
        // showV2();  default
        // showV3();
        // showV4();

        // 以下代码：D 作为与 A 同包平等的身份访问 A 的成员
        // 由于不在一个包，且非 A 子类，只能访问 public 成员
        A obj = new A();
        // System.out.println(obj.v1);
        // System.out.println(obj.v2);
        // System.out.println(obj.v3);
        System.out.println(obj.v4);
        // obj.showV1();
        // obj.showV2();
        // obj.showV3();
        obj.showV4();
    }
}
```

### `Java` 访问权限总结

- `Java` 属性和方法有四种权限
  - `private`
  - `default`
  - `protected`
  - `public`
- 个人推荐
  - 成员变量都是 `private`
  - 成员方法都是 `public`