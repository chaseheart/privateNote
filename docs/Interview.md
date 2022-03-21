# 面试题

## 基础篇

### Java语言有哪些特点

1. 简单易学、有丰富的类库
2. 面向对象
3. 可跨平台
4. 可靠安全
5. 支持多线程



------

### 面向对象和面向过程的区别

**面向过程**：分析解决问题的步骤，然后用函数将步骤逐步实现，再在使用的时候调用。性能较高，所以单片机、嵌入式开发等一般采用面向过程开发。

**面向对象**：把构成问题的事务分解成各个对象，描述某个事物在解决整个问题过程中所发生的行为。面对对象有封装、继承、多态的特性，所以易维护、易复用、易扩展。可以设计出低耦合的系统。但从性能上来说，比面向过程要低。



------

### 基本数据类型

| 基本类型 | 大小（字节） | 默认值       | 封装类    |
| -------- | ------------ | ------------ | --------- |
| byte     | 1            | (byte)0      | Byte      |
| short    | 2            | (short)0     | Short     |
| int      | 4            | 0            | Integer   |
| long     | 8            | 0L           | Long      |
| float    | 4            | 0.0f         | Float     |
| double   | 8            | 0.0d         | Double    |
| boolean  | -            | false        | Boolean   |
| char     | 2            | \u0000(null) | Character |



------

### 标识符的命名规则

**命名规则:(硬性要求)**
标识符可以包含英文字母，0-9的数字，$以及_ 标识符不能以数字开头 标识符不是关键字 .

**命名规范:(非硬性要求)**
类名规范：首字符大写，后面每个单词首字母大写(大驼峰式).
变量名规范：首字母小写，后面每个单词首字母大写(小驼峰式)。 方法名规范：同变量名.



------

### instanceof 关键字的作用

```java
boolean result = obj instanceof Class
```

> 其中 obj 为一个对象，Class 表示一个类或者一个接口，当 obj 为 Class 的对象，或者是其直接或间接子类，或者是其接口的实现类，结果result 都返回 true，否则返回false。 注意：编译器会检查 obj 是否能转换成右边的class类型，如果不能转换则直接报错，如果不能确定类型，则通过编译，具体看运行时定。

```java
int i = 0;
System.out.println(i instanceof Integer);//编译不通过 i必须是引用类型，不能是基本类型
System.out.println(i instanceof Object);//编译不通过
```

```Java
Integer integer = new Integer(1);
System.out.println(integer instanceof  Integer);//true
```

```java
//false ,在 JavaSE规范 中对 instanceof 运算符的规定就是：如果 obj 为 null，那么将返回false。
System.out.println(null instanceof Object);
```



------

### 装箱拆箱

> **装箱**就是自动将基本数据类型转换为包装器类型(int–>Integer);调用方法：Integer的valueOf(int)方法
> **拆箱**就是自动将包装器类型转换为基本数据类型(Integer–>int).调用方法：Integer的intValue方法

```java
public class PackegeClass {
    public static void main(String[] args) {
        int a = 12 ;
        Integer a1 = 12 ;  // 自动装箱
        Integer a2 = a ;   // 自动装箱
        Integer a3 = null; // 引用数据类型的默认值可以为null

        Integer c = 100 ;
        int c1 = c ;      // 自动拆箱

        Integer it = Integer.valueOf(12);  	// 手工装箱！
        // Integer it1 = new Integer(12); 	// 手工装箱！
        Integer it2 = 12;

        Integer it3 = 111 ;
        int it33 = it3.intValue(); // 手工拆箱
    }
}
```

**自动装箱**反编译后底层调用 `Integer.valueOf()` 实现，源码：

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        // 【缓存池】，本质上是一个数组
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

自动拆箱调用 `java.lang.Integer#intValue`，源码：

```java
public int intValue() {
    return value;
}
```



------

### 缓存池

new Integer(123) 与 Integer.valueOf(123) 的区别在于：

- new Integer(123)：每次都会新建一个对象

- Integer.valueOf(123)：会使用缓存池中的对象，多次调用取得同一个对象的引用

  ```java
  Integer x = new Integer(123);
  Integer y = new Integer(123);
  System.out.println(x == y);    // false
  Integer z = Integer.valueOf(123);
  Integer k = Integer.valueOf(123);
  System.out.println(z == k);   // true
  ```

valueOf() 方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。

**基本类型对应的缓存池如下：**

- Boolean values true and false
- all byte values
- Short values between -128 and 127
- Long values between -128 and 127
- Integer values between -128 and 127
- Character in the range \u0000 to \u007F (0 and 127)

在 jdk 1.8 所有的数值类缓冲池中，**Integer 的缓存池 IntegerCache 很特殊，这个缓冲池的下界是 -128，上界默认是 127**，但是上界是可调的，在启动 JVM 时通过 `AutoBoxCacheMax=<size>` 来指定这个缓冲池的大小，该选项在 JVM 初始化的时候会设定一个名为 java.lang.IntegerCache.high 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界

```java
Integer x = 100;				// 自动装箱，底层调用 Integer.valueOf(1)
Integer y = 100;
System.out.println(x == y);   	// true

Integer x = 1000;
Integer y = 1000;
System.out.println(x == y);   	// false，因为缓存池最大127

int x = 1000;
Integer y = 1000;
System.out.println(x == y);		// true，因为 y 会调用 intValue 【自动拆箱】返回 int 原始值进行比较
```



------

### 重载和重写的区别

