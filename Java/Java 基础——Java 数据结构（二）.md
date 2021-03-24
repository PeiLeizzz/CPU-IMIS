---
title: Java 基础——Java 数据结构（二）
date: 2020-06-06 13:33:10
tags: 
- Java
categories:
- Java
toc: true
reward: true
---

<!--more-->

## 列表（`List`）

- `List`：列表 
  - **有序**的 `Collection`
  - **允许重复元素**
  - `{1，2，4，{5，2}，1，3}`

- 主要实现 
  - **`ArrayList(非同步的)`**
  - **`LinkedList(非同步)`**
  - **`Vector(同步)`**

### `ArrayList`

- 以**数组**实现的列表，不支持同步
  - 实现同步语法：`List list = Collections.synchronizedList(new ArrayList(...)); `
- 利用**索引**位置可以快速定位访问 
- **不适合**指定位置的插入、删除操作$(O(N))$
- 适合**变动不大**，主要用于**查询**的数据
- 和 `Java` 数组相比，其**容量**是可**动态调整**的 
- `ArrayList` 在元素填满容器时会**自动扩充容器大小的 50%**

### `LinkedList`

- 以**双向链表**实现的列表，不支持同步
  - 实现同步语法：`List list = Collections.synchronizedList(new LinkedList(...));`
- 可被当作堆栈、队列和双端队列进行操作 
- **顺序访问高效，随机访问较差，中间插入和删除高效** 
- 适用于**经常变化**的数

### `Vector`（同步）

- 和 `ArrayList` 类似，**可变数组**实现的列表
- `Vector` **同步**，适合在**多线程**下使用
- 原先不属于 `JCF` 框架，属于 `Java` 最早的数据结构，**性能较差**
- 从 `JDK1.2` 开始，`Vector` 被重写，并纳入到 `JCF`
- 官方文档建议在**非同步**情况下，**优先采用 `ArrayList`** 

---

### 不同列表的遍历方法

#### `Iterator` 迭代器遍历法

```java
Iterator<Integer> iter = l.iterator();
while (iter.hasNext()) {
	System.out.println(iter.next());
}
```

> **构建`Iterator`迭代器语法**：`Iterator<T> iter = obj.iterator();`
>
> `ArrayList`，`LinkedList`，`Vector`都有的遍历方法
>
> 速度较慢

#### 通过 `index` 索引遍历

```java
for (int i = 0; i < l.size(); i++) {
    System.out.println(l.get(i));
}
```

> `ArrayList`，`LinkedList`，`Vector`都有的遍历方法
>
> **对于`ArrayList`较快，而`LinkedList`较慢**

#### 通过强化 `for-each` 遍历

```java
for (item: l) {
    System.out.println(item);
}
```

> `ArrayList`，`LinkedList`，`Vector`都有的遍历方法

#### 通过 `Enumeration` 遍历

```java
for (Enumeration<Integer> enu = l.elements(); enu.hasMoreElements(); ) {
	System.out.println(enu.nextElement());
}
```

> **构建`Enumeration`接口的语法**：`Enumeration<T> enu = obj.elements();`
>
> `Vector`特有的遍历方法
>
> 不常用

## 集合（`Set`）

- 集合特性：
  - **确定性**：对任意对象都能判定其是否属于某一个集合
  - **互异性**：集合内每个元素都是无差异的，注意是**内容差异**
  - **无序性**：集合内的**顺序无关**
- `Java` 中的集合接口 `Set`
  - **HashSet**（基于**散列**函数的集合，**无序**，不支持同步）
  - **LinkedHashSet**（基于**散列函数和双向链表**的集合，**可排序**的，不支持同步）
  - **TreeSet**（基于**树**结构的集合，**可排序**的，不支持同步）

### `HashSet`

- 基于 `HashMap` 实现的，**可以容纳 `null` 元素**, 不支持同步
  - 实现同步语法：`Set s = Collections.synchronizedSet(new HashSet(...));`
- `add` 添加一个元素
- `clear` 清除整个`HashSet`
- `contains` 判定是否包含一个元素
- `remove` 删除一个元素 
- `size` 大小
- `retainAll` 计算两个集合**交集**

### `LinkedHashSet`

- 继承 `HashSet`，也是基于 `HashMap` 实现的，**可以容纳 `null` 元素**，不支持同步
  - 实现同步语法：`Set s = Collections.synchronizedSet(new LinkedHashSet(...));`
- 方法和 `HashSet` 基本一致
  - `add, clear, contains, remove, size`
- 通过一个双向链表维护插入顺序

### `TreeSet`

- 基于 `TreeMap` 实现的，**不可以容纳 `null` 元素**，不支持同步
  - 实现同步语法：`SortedSet s = Collections.synchronizedSortedSet(new TreeSet(...)); `
- `add` 添加一个元素 
- `clear` 清除整个`TreeSet` 
- `contains` 判定是否包含一个元素 
- `remove` 删除一个元素
- `size` 大小 
- **根据 `compareTo` 方法或指定 `Comparator` 排序** 

---

### 集合的遍历

和列表的遍历类似，但是这三个集合类都不能用 `Enumeration` 遍历

---

### 判定重复元素

#### HashSet 和 LinkedHashSet 判定元素重复的原则 

- 判定两个元素的 `hashCode` 返回值是否相同，若不同，返回 `false`
- 若两者 `hashCode` 相同，判定 `equals` 方法，若不同，返回 `false`；否则返回 `true`
- `hashCode` 和 `equals` 方法是所有类都有的，因为 `Object` 类有 

```java
// Dog 类
public class Dog {
    private int size;

    public Dog(int s) {
        size = s;
    }

    public int getSize() {
        return size;
    }

    // 重写
    public boolean equals(Object obj2) {
        System.out.println("Dog equals()~~~~~~~~~~~~~~");
        // Dog 对象传进来 先向上转型变为 Object 再向下转型变回 Dog
        if (size == ((Dog)obj2).getSize()) {
            return true;
        }
        else {
            return false;
        }
    }

    // 重写
    public int hashCode() {
        System.out.println("Dog hashCode()~~~~~~~~~~~~");
        // hashCode 由 size 决定
        return size;
    }

    // 一般改写了上面的两个方法，toString() 最好也改写
    // 这三个方法通常是三位一体的
    // equals() 相同；hashCode() 相同，那么 toString() 也应该相同
    public String toString() {
        System.out.println("Dog toString()~~~~~~~~~~~~");
        return size + " ";
    }
}

public class ObjectHashSetTest {
    public static void main(String[] args) {
        HashSet<Dog> hs2 = new HashSet<Dog>();
        hs2.add(new Dog(2));
        hs2.add(new Dog(1));
        hs2.add(new Dog(3));
        hs2.add(new Dog(5));
        // 下面两个 Dog 对象相同
        hs2.add(new Dog(4));
        hs2.add(new Dog(4));
        System.out.println(hs2.size()); // 5
    }
}

输出：
    Dog hashCode()~~~~~~~~~~~~
    Dog hashCode()~~~~~~~~~~~~
    Dog hashCode()~~~~~~~~~~~~
    Dog hashCode()~~~~~~~~~~~~
    Dog hashCode()~~~~~~~~~~~~
    Dog hashCode()~~~~~~~~~~~~
    Dog equals()~~~~~~~~~~~~~~   <--当 hashCode 相同时才比较 equals
    5
```

---

#### <span id="jump">`TreeSet` 判定元素重复的原则 </span>

- **需要元素实现 `Comparable` 接口的 `compareTo` 方法（可以修改类的情况下）**
- **或者采用定制的 `Comparator` 方式（不可以修改类的情况下）**
- **非系统提供的类必须实现其中一个**，若不实现，则**按加入顺序**存储

##### 实现 `Comparable` 接口的 `compareTo` 方法（自动调用）

```java
// Tiger 类
// 注意这里如果不写 Comparable<Tiger>，下面的 CompareTo()方法形参类型只能为 Object，函数内部还需向下转型
public class Tiger implements Comparable {
    private int size;

    public Tiger(int s) {
        size = s;
    }

    public int getSize() {
        return size;
    }

    public int compareTo(Object o) {
        System.out.println("Tiger compareTo()~~~~~~");
        // return > 0, obj1 > obj2
        // return 0, obj1 == obj2
        // return < 0, obj1 < obj2
        return size - ((Tiger) o).getSize();
    }
}

public class ObjectTreeSetTest {
    public static void main(String[] args) {
        TreeSet<Tiger> ts = new TreeSet<Tiger>();
        ts.add(new Tiger(2));
        ts.add(new Tiger(1));
        ts.add(new Tiger(3));
        ts.add(new Tiger(5));
        // 下面两个 Tiger 对象相同
        ts.add(new Tiger(4));
        ts.add(new Tiger(4));
        System.out.println(ts.size()); // 5
    }
}

输出：
    Tiger compareTo()~~~~~~
    Tiger compareTo()~~~~~~
    Tiger compareTo()~~~~~~
    Tiger compareTo()~~~~~~
    Tiger compareTo()~~~~~~
    Tiger compareTo()~~~~~~
    Tiger compareTo()~~~~~~
    Tiger compareTo()~~~~~~
    Tiger compareTo()~~~~~~
    Tiger compareTo()~~~~~~
    5
```

##### 采用 `Comparator` 方式（需要作为参数）

```java
// Tiger 类
public class Tiger {
    private int size;
    
    public Tiger(int s) {
        size = s;
    }

    public int getSize() {
        return size;
    }
}

public class ObjectTreeSetTest {
    public static void main(String[] args) {
        // 也可以写成一个单独的类 implements Comparator
        TreeSet<Tiger> ts = new TreeSet<Tiger>(new Comparator<Tiger>() {
            public int compare(Tiger t1, Tiger t2) {
                System.out.println("Tiger compare()~~~~~~");
                return t1.getSize() - t2.getSize();
            }
        });
        ts.add(new Tiger(2));
        ts.add(new Tiger(1));
        ts.add(new Tiger(3));
        ts.add(new Tiger(5));
        // 下面两个 Tiger 对象相同
        ts.add(new Tiger(4));
        ts.add(new Tiger(4));
        System.out.println(ts.size()); // 5
    }
}

输出：
    Tiger compare()~~~~~~
    Tiger compare()~~~~~~
    Tiger compare()~~~~~~
    Tiger compare()~~~~~~
    Tiger compare()~~~~~~
    Tiger compare()~~~~~~
    Tiger compare()~~~~~~
    Tiger compare()~~~~~~
    Tiger compare()~~~~~~
    Tiger compare()~~~~~~
    5
```

---

## 映射（`Map`）

- `Map` 映射
  - 数学定义：两个集合之间的元素对应关系。 
  - 一个输入对应到一个输出 
  - {1，张三}，{2，李四}，{Key，Value}，**键值对**，K-V 对
- `Java` 中 `Map` 
  - `Hashtable`**（同步，慢，数据量小）**
  - `HashMap`**（不支持同步，快，数据量大）**
  - `LinkedHashMap `**（有序）**
  - `TreeMap`**（有序）**
  - `Properties` **(同步，文件形式，数据量小)**

### `Hashtable`

- K-V 对，**K 和 V 都不允许为 `null`**
- 同步，多线程安全
- **无序**的
- 适合小数据量
- 主要方法：`clear, contains/containsValue, containsKey, get, put, remove, size`

### `HashMap`

- K-V 对，**K 和 V 都允许为 `null`**
- 不同步，多线程不安全
  - 实现同步语法：`Map m = Collections.synchronizedMap(new HashMap(...));`
- **无序**的
- 主要方法：`clear, containsValue, containsKey, get, put, remove, size `

###  `LinkedHashMap`

基于**双向链表**的维持**插入顺序**的 `HashMap `

### `TreeMap`

基于红黑树的 `Map`，可以根据 `key` 的**自然排序**或者** `compareTo` 方法**进行排序输出

### `Properties`

- 继承于 `Hashtable`
- 可以将 K-V 对保存在文件中
- 适用于数据量少的配置文件
- 继承自 `Hashtable` 的方法：`clear, contains/containsValue, containsKey, get, put, remove, size`
- 从文件加载的 `load` 方法， 写入到文件中的 `store` 方法
- 获取属性 `getProperty`，设置属性 `setProperty`

---

### `Map` 的遍历

#### 通过 `Entry` 遍历

```java
Iterator<Entry<Integer, String>> iter = ht.entrySet().iterator();
while (iter.hasNext()) {
    Entry<Integer, String> entry = iter.next();
    // 获取 key
    key = entry.getKey();
    // 获取 value
    value = entry.getValue();
    // System.out.println("Key: " + key + ", Value " + value);
}
```

> **构建 `Entry` 接口的 `Iterator` 迭代器的语法**：`Iterator<Map.Entry<T1, T2>>iter = obj.entrySet().iterator();`
>
> `Hashtable`，`HashMap`，`LinkedHashMap`，`TreeMap` 都有的遍历方法

#### 通过 `keySet` 遍历

```java
Iterator<Integer> iter = hm.keySet().iterator();
while (iter.hasNext()) {
    // 获取 key
    key = iter.next();
    // 获取 value
    value = hm.get(key);
    // System.out.println("Key: " + key + ", Value " + value);
}
```

> **通过调用`keySet`方法得到`Key`集合的`Iterator`迭代器的语法**：`Iterator<T> iter = obj.keySet().iterator();`
>
> `Hashtable`，`HashMap`，`LinkedHashMap`，`TreeMap` 都有的遍历方法

#### 通过 `Enumeration` 遍历

```java
Enumeration<Integer> keys = ht.keys();
while (keys.hasMoreElements()) {
    // 获取 key
    key = keys.nextElement();
    // 获取 value
    value = ht.get(key);
    // System.out.println("Key: " + key + ", Value " + value);
}
```

> **构建 `Key` 的 `Enumeration` 遍历的语法**：`Enumeration<T> keys = obj.keys();`
>
> `Hashtable` 特有的遍历方法

#### 总结

其实只有 `Entry` 是对 **整个 `Map`** 的遍历，其他两种方法 `keySet` 和 `Enumeration` 都是对 key 的遍历

---

### `TreeMap` 制定排序规则

与上节[TreeSet](#jump)类似，在此不多赘述。

---

### `Properties` 与文件的交互

> 在此只做样例的列示（部分 `API` 的调用给出详细的注释），具体的 `Java IO` 操作，将于之后在其他文章中详解

```java
import java.io.*;
import java.util.Enumeration;
import java.util.Properties;

public class PropertiesTest {
    // 写入 Properties 信息
    public static void WriteProperties (String filePath, String pKey, String pValue) throws IOException {
        File file = new File(filePath);
        if (!file.exists()) {
            file.createNewFile();
        }
        Properties pps = new Properties();

        InputStream in = new FileInputStream(filePath);
        // 从输入流中读取属性列表（键和元素对）
        // load 是加载源文件中所有的键值对到 pps
        pps.load(in);
        // 调用 Hashtable 的方法 put，使用 getProperty 方法提供并行性，getProperty()：获取某一个 Key 对应的 Value
        // 强制要求为属性的键和值使用字符串，返回值是 Hashtable 调用 put 的结果
        OutputStream out = new FileOutputStream(filePath);
        // 向 pps 写入一个键值对
        pps.setProperty(pKey, pValue);
        // 以适合使用 load 方法加载到 Properties 表中的格式
        // 将此 Properties 表中的属性列表（键和元素对）写入输出流
        // store 是将所有的键值对写入文件 out
        pps.store(out, "Update " + pKey + " name");
        out.close();
    }

    // 读取 Properties 的全部信息
    public static void GetAllProperties(String filePath) throws IOException {
        Properties pps = new Properties();
        InputStream in = new BufferedInputStream(new FileInputStream(filePath));
        pps.load(in);
        Enumeration en = pps.propertyNames(); // 得到配置文件的名字

        while (en.hasMoreElements()) {
            String strKey = (String) en.nextElement();
            String strValue = pps.getProperty(strKey);
            // System.out.println(strKey + " = " + strValue);
        }
    }

    // 写入 Properties 信息
    public static String GetValueByKey(String filePath, String key) {
        Properties pps = new Properties();
        try {
            InputStream in = new BufferedInputStream(new FileInputStream(filePath));
            pps.load(in);
            String value = pps.getProperty(key);
            // System.out.println(key + " = " + value);
            return value;
        }
        catch (IOException e) {
            e.printStackTrace();
            return null;
        }
    }

    public static void main(String[] args) throws IOException {
        System.out.println("写入 Test.properties==========");
        WriteProperties("Test.properties", "name", "12345");

        System.out.println("加载 Test.properties==========");
        GetAllProperties("Test.properties");

        System.out.println("从 Test.properties 指定 Key 加载==");
        String value = GetValueByKey("Test.properties", "name");
        System.out.println("name is " + value);
    }
}

输出：
    写入 Test.properties==========
    加载 Test.properties==========
    从 Test.properties 指定 Key 加载==
    name is 12345
```

### `Map` 注意点

- `HashMap` 是最常用的映射结构
- 如需要排序，考虑 `LinkedHashMap` 和 `TreeMap`
- 如需要将 K-V 存储为文件，可采用 `Properties` 类

## 工具类（`Arrays` 和 `Collections`）

### `Arrays`

- **处理对象是数组**
  - **排序**：对数组排序， `sort/parallelSort`
  - **查找**：从数组中查找一个元素，`binarySearch`（不存在则返回 `-1`，如果存在多个，不保证返回哪个）
  - **批量拷贝**：从源数组批量复制元素到目标数组，`copyOf`
  - **批量赋值**：对数组进行批量赋值，`fill` 
  - **等价性比较**：判定两个数组内容是否相同，`equals` 

### `Collections`

- **处理对象是 `Collection` 及其子类**
  - **排序**：对 `List` 进行排序，`sort`
  - **搜索**：从 `List` 中搜索元素，`binarySearch`
  - **批量赋值**：对 `List` 批量赋值，`fill`
  - **最大、最小**：查找集合中最大/小值，`max, min`
  - **反序**：将 `List` 反序排列，`reverse`

### 对象比较

- 对象实现 `Comparable` 接口（**需要修改对象类**）
  - `compareTo` 方法 
    - `>` 返回 `1`，`==` 返回 `0`，`<` 返回 `-1`
  - `Arrays` 和 `Collections` 在进行对象 `sort` 时，**自动**调用该方法
- 新建 `Comparator`（**适用于对象类不可更改的情况**） 
  - `compare` 方法 
    - `>` 返回 `1`，`==` 返回 `0`，`<` 返回 `-1`
  - `Comparator` 比较器将**作为参数**提交给工具类的 `sort` 方法 

#### 实现 `Comparable` 接口的 `compareTo` 方法（自动调用）

> 这里的 `Comparable` 接口的例子实现与[TreeSet](#jump)中略有不同，此处的 `Comparable` 后面加上了 `<T>`，这样在 `compareTo` 方法中，形参就可以是具体的 `T` 类，函数内部不需要再向下转型。

```java
public class Person implements Comparable<Person> {
    String name;
    int age;

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 从小到大排序
    public int compareTo(Person another) {
        int i = 0;
        // 注意这里的 compareTo 是 String 类中的方法
        i = name.compareTo(another.name); // 使用字符串比较
        if (i == 0) {
            // 如果名字一样，比较年龄
            return age - another.age;
        }
        else {
            // 名字不一样，就返回比较名字的结果
            return i;
        }
    }

    public static void main(String[] args) {
        Person[] ps = new Person[3];
        ps[0] = new Person("Tom", 20);
        ps[1] = new Person("Mike", 18);
        ps[2] = new Person("Mike", 20);

        Arrays.sort(ps);
        for (Person p: ps) {
            System.out.println(p.getName() + ", " + p.getAge());
        }
    }
}

输出：
    Mike, 18
    Mike, 20
    Tom, 20
```

#### 采用 `Comparator` 方式（需要作为参数）

> 这里 `Comparator` 接口实现的例子实现与[TreeSet](#jump)中略有不同，[TreeSet](#jump)中采用的是**匿名类**的方式，而此处则使用外部类方式。

```java
// Person2 类
public class Person2 {
    private String name;
    private int age;
    public String getName() {
        return name;
    }
    public int getAge() {
        return age;
    }
    public Person2(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

class Person2Comparator implements Comparator<Person2> {
    public int compare(Person2 one, Person2 another) {
        int i = 0;
        i = one.getName().compareTo(another.getName());
        if (i == 0) {
            return one.getAge() - another.getAge();
            // 降序
            // return another.getAge() - one.getAge();
        }
        else {
            return i;
        }
    }
}

public class Person2ComparatorTest {
    public static void main(String[] args) {
        Person2[] ps = new Person2[3];
        ps[0] = new Person2("Tom", 20);
        ps[1] = new Person2("Mike", 18);
        ps[2] = new Person2("Mike", 20);

        Arrays.sort(ps, new Person2Comparator());
        for (Person2 p: ps) {
            System.out.println(p.getName() + ", " + p.getAge());
        }
    }
}

输出：
    Mike, 18
    Mike, 20
    Tom, 20
```

