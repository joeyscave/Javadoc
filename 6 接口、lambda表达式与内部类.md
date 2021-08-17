## 6.1 接口

### 6.1.1 接口的概念

> 只要你实现了某个特定接口，我就可以履行服务。Arrays<T>类的sort服务需要实现T类实现iComparable接口

1. 接口类似于纯抽象类的概念，但接口不能有实例字段，且接口中的方法默认为public abstract
2. 实现接口implements、对接口中的所有方法进行定义
3. 书里的例子值得一看
4. 注意反对称问题(继承里也有类似的反对称问题)

### 6.1.2 接口的属性

> 基本上跟抽象类属性一致，接口的出现主要为了解决抽象类没法多继承的问题

1. 不能实例化一个接口
2. 能声明接口类型的变量，该变量只能引用实现了该接口的类
3. 可以用InstanceOf检查一个类是否实现了某个接口
4. 接口可以继承(扩展)不止一个其他接口，用extends关键字
5. 接口中不能包含实例字段，但可以包含常量，常量默认为public final static

### 6.1.4 静态和私有方法

1. 在java8后接口中可以定义非抽象的静态方法，这样静态方法就不用放到接口的伴随类里去了(以往的做法)
2. 在java9后接口中的方法可以是私有的，但是只能为接口中其他方法所用，用法很有限

### 6.1.5 默认方法

1. default修饰符为接口提供一个默认实现，可以在默认方法内写方法体，实现接口的类可以不定义默认方法。
2. 默认方法的另一个重要用法是"接口演化"，解决兼容问题。

### 6.1.6 解决默认方法冲突

多继承时(超类加上所有接口中的默认方法)若出现同签名的方法，按什么规则解决冲突？

1. 超类优先

2. 接口冲突时：只要这些冲突的方法中任意一个是默认方法，就需要在实现接口的类中重写这个方法

   ```java
   class Student implements Person,Named{
       public String getname(){ return Person.super.getName();}
   }
   //可以如上调用接口里的默认方法，不用完全重写
   ```

### 6.1.7 接口与回调

> 回调(call back)指在特定动作(比如鼠标点击，时间过了一秒等)后执行相应的响应

不用告诉定时器响应所对应的方法，而是要给它一个对象。这个对象是实现了ActionListener接口的，这个接口内的方法定义了响应所对应的方法。

### 6.1.8 Comparator接口

因为String类的CompareTo已经被重写了成按字典序比较了，自定义String对象的比较规则可以使用Comparator接口

### 6.1.9 对象克隆

1. 普通拷贝只是让两个变量引用同一个对象，而不是创造副本，想要创造副本，需要使用.clone方法

2. object.clone方法只是简单地拷贝对象中的所有字段，对于基本类型可以创造副本，但对于对象，实际上是在拷贝引用，这种拷贝称之为 *浅拷贝*

3. 要建立真正创造全新副本的 *深拷贝* ，必须在类中重写clone方法

   ```java
      public Employee clone() throws CloneNotSupportedException
      {
         // call Object.clone() 这一步完成浅拷贝，接下来要对类里的对象字段进行拷贝
         Employee cloned = (Employee) super.clone();
   
         // clone mutable fields 调用对象字段的clone方法拷贝对象字段（对象字段的clone方法需先完成深拷贝）
         cloned.hireDay = (Date) hireDay.clone();
   
         return cloned;
      }
   ```

4. 接口cloneable是一个 *标记接口 (tagging interface)* ，里面什么都没有，只是表明实现了该接口的类重写了clone而已。如果没有实现该接口而直接调用clone(类内实际上是有clone方法的)，会抛出一个检查异常(即上文代码块里的异常)

5. object.clone是protected的，这意味着如果没有重写clone并用public修饰，不能随便调用类的clone方法

6. 小心重写了clone类的子类，如果子类的clone没有重写，那子类仍然可能会有部分字段是浅拷贝的

## 6.2 lamda表达式

### 6.2.1 为什么引入lamda

许多API要的接口类型(可以声明接口类型的变量)参数都实际上必须要接收一个实现了该接口的类，因此要特地写一个类实现接口中的方法，然而这个参数实际上想要的只是一个方法而已，用lamda就可以直接把这个方法给API

### 6.2.2 lamda语法

```java
//不用写修饰符(必然为public) 不用写返回类型(编译器会根据内容推导)
(String s)->{
    int i=s.length();
    return i/2;
};

//没有参数时要写一个括号
()->{
    ...
    ...
};

//大部分情况编译器可以根据被lamda表达式赋值的接口推导参数的类型
//此时就不需要写参数类型和圆括号了
s->{
    ...
    ...
};

//当方法体只有一行时不用写大括号和return
s->s.length();

//lamda变量的类型是接口
接口名 variable=lamda expression;
```

1. 为什么使用函数式接口接口作为lamda变量的类型，而不是重新定义一个lamda类型？

   > 主要是解决兼容性的问题。这样之前那些需要接口作为参数的API就不用重写了

2. 如果lamda表达式只在某些分支返回一个值，是不合法的。

   ```java
   (int x)->{
       if (x>=0)
           return 1;
   }
   //不合法
   ```

### 6.2.3 函数式接口

> 只有一个抽象方法的接口为函数式接口

实例：

```java
ArrayList list=...;
list.removeIf(e->e==null);	//接受一个Predicate接口参数，这里直接传入一个lamda表达式

supplier //没搞懂这个例子777
```

### 6.2.4 方法引用

如果lamda表达式的内容仅仅只是调用一个方法，那么可以使用方法引用：

```java
var timer=new Timer(1000,event->System.out.println(event));	//lamda
var timer=new Timer(1000,System.out::printLn);	//方法引用
```

1. 当调用的方法有多个重载时，根据接口(中方法)的参数类型选择对应的参数类型的方法来调用

2. 用 :: 分隔方法名与对象或类名，主要有三种情况：

   + object::instanceMethod    //(object表示对象，不是object类)

     > 调用方法的参数即为接口参数
     >
     > ```java
     > System.out::println 等价于 x->System.out.println(x);
     > ```

   + Class::instanceMethod

     > 接口的第一个参数为调用方法的隐式参数，相当于这样实现接口：
     >
     > ```java
     > class extends interface0{
     >     int compare(String x,String y){ 			//接口签名
     >         return x.compare(y);  		//调用的方法
     >     }
     > }
     > ```

   + Class::staticMethod

     > 所有参数都传递到静态方法
     >
     > ```java
     > class extends interface1{
     >     Double powIt(Double x,Double y){ 			//接口签名
     >         return pow(x,y);  		//调用的方法
     >     }
     > }
     > ```

3. 可以使用this和super   p249

### 6.2.5 构造器引用

先过了777

### 6.2.6 变量作用域

1. lamda表达式中捕获的变量(使用的外部变量)必须实际上是事实最终变量(这个变量初始化后就不会再为它赋值)

2. lamda表达式的体相当于一个嵌套的语句块，适用嵌套语句块的各种规则，比如不能声明与外部语句块同名的变量

3. lamda表达式中的this指的是创建lamda表达式的方法的this隐式参数(一般代表类的某个实例),而不是接口的实例

   <img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210722112749218.png" alt="image-20210722112749218" style="zoom:80%;" />

   > 以上代码中的this指的是Application实例

### 6.2.7 处理lamda表达式

一个函数式接口的主要特点就是其所包含的抽象方法签名，如果签名相同，方法换个名字、接口换个名字，其实还是同一个接口，基于此，开发库里定义了许多常用签名的函数式接口，这样需要用到某类签名的接口时就不用自己创建接口了

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210722124736722.png" alt="image-20210722124736722" style="zoom:80%;" />

> 记忆小技巧：什么都不需要，什么也不给，单纯运行，run；不接收，但是给出东西，供应，supplier；接收但不给东西，消费，consumer；接受东西，给出另外种类的东西，标准函数，function；给出布尔值，预言家，predicate

IntConsumer比Consumer<Interger>效率更高，类似的基本类型接口：

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210722125842100.png" alt="image-20210722125842100" style="zoom:80%;" />

为自己设计的函数式接口使用@FunctionalInterface注解

### 6.2.8 再谈Comparator 接口

先过了777

## 6.3 内部类

使用内部类主要有两个原因：
+ 内部类可以对同一个包中的其他类隐藏
+ 内部类方法可以访问定义这个类的作用域中的数据，包括原本私有的数据

### 6.3.1 使用内部类访问对象状态

内部类可以直接访问外部类的对象状态，即使是private

### 6.3.2 内部类的特殊语法规则

内部类先过了777

## 6.4服务加载器

过了777

## 6.5 代理

过了777