## 3.3 数据类型

### 3.3.1 整型

+ int
+ long
+ byte
+ short

1. 前后缀
2. 整型范围与机器无关
3. 可以在数字中加下划线
4. 没有无符号类型（unsigned)   对此注释中有一段说明

### 3.3.2 浮点型

+ float(4 bytes)(有效位数6~7)

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210627161924303.png" alt="image-20210627161924303" style="zoom:80%;" />

+ double(8 bytes)（有效位数15）

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210627162004218.png" alt="image-20210627162004218" style="zoom:80%;" />

1. 上图的表示方法为IEEE 754规范

2. + 下溢：用非规格化数表示解决（具体参见计组附录B）
   + 上溢：用无穷大表示（阶码全为1，尾数为0）
   + NaN (not a number)：0除以0或者无穷除以无穷

3. 1.0f为单精度，1.0d为双精度，无符号时默认双精度

4. 双精度正无穷大(Double.POSITIVE_INFINITY)，其他同理

5. ```java
   if (x==Double.NaN)		//错误
   ```

   不能用以上方法判断x是否(not a number)

   ```java
   if (Double.isNaN(x))	//正确
   ```

6. 浮点数运算将产生舍入误差，例如2.0-1.1将打印为0.899999999

7. p31中间的注释0.125的表示没看懂777

### 3.3.3 char类型

1. java中char类型占2字节，可以存储一个16位的unicode字符

2. *'\u2122'*    unicode字符如此表示

3. 类似\n的转义序列也可以用unicode表示

4. > 需要注意的地方

```java
"\u0022+\u0022"		
//\u会在编译时就被转换，所以应该是""+""，一个空串，而不是"+"

//\u000a is a newline
//注释中的\u也会被替换，上述字符替换后将产生换行符，语法报错
    
//\users
//错误，\u后面没有跟四位数字
```

### 3.3.4 unicode和char类型

1. *U+0000* 到 *U+FFFF* 称为基本多语言平面

2. *U+10000* 到 *U+FFFFF* 为其余16个平面，包含辅助字符

3. 码点、代码单元、UTF-16、区分单个字符和辅助字符算法等概念777

   > 粗浅理解：代码单元为两字节16位，一个char型变量保存一个代码单元，而码点（code point）则是字符在字符集中对应的编码，可能超过16位（辅助字符），UTF-16则是通过一定的算法，把超过16位的辅助字符转换成两个代码单元，且这两个代码单元的取值为基本字符集未用的位置（*U+D800~U+DBFF*用于第一个代码单元，*U+DC00~U+DFFF* 用于第二个代码单元)这样做的好处是不需要另外存储一个标记来表示是基本字符还是辅助字符，可以迅速知道一个代码单元表示基本字符还是辅助字符的第一个或是第二个代码单元

4. 不建议使用char

### 3.3.5 boolean类型

> 在java中布尔类型和整型不能相互转化

```java
if (x=0)	//在c++中永远false，在java中则编译不通过
```

## 3.4 变量与常量

### 3.4.1 变量声明

1. 变量名可以包括字母（任何代表字母的unicode字符）和数字
2. 大小写敏感

### 3.4.2 变量初始化

1. 必须对变量进行显式初始化
2. 良好风格：对变量的声明尽量放在靠近第一次使用它的地方
3. java不区分变量的声明和定义

### 3.4.3 常量

1. 使用关键字 *final* 定义常量
2. 类常量：使用 *static final* 定义在类中，类的main方法之外，相当于一个类的全局常量，类中所有方法都可使用这个常量，当声明为public时，其他类也可使用此常量

~~~java
public class constant{
    public static final Constant;
    
    public static void main(){};
}
~~~

### 3.4.4 枚举类型

1. 应用场景：变量取值在有限集合内，不希望变量被赋其他值

2. ```java
   enum Size{SMALL,MEDIUM,LARGE};
   Size s=Size.MEDIUM;
   ```

   > 其中*Size*是自定义的枚举类型

3. 枚举类型变量除了取集合中的值只能取NULL，表示没有值

## 3.5 运算符

### 3.5.1 算数运算符

1. 整数被0除会产生一个异常，而浮点数被0除会得到无穷大或NaN
2. p37注释中谈到了关于浮点数计算可移植性的问题

### 3.5.2 数学函数与常量

1. Math类中包含了各种数学函数
2. 在源文件顶部加上声明后方法前可忽略Math

```java
import static java.lang.Math.*;
```

3. 普通取余和floorMod取余

```java
(-2)%8=-2
Math.floorMod(-2,8)=-6
```

4. Math类所有方法使用计算机浮点单元中的例程。如果得到可预测（可移植性）的结果比性能更重要，应使用StrictMath类

5. Math类提供一些方法使整数运算有更好的安全性

   > 例如十亿*3会产生上溢，悄无声息地输出错误结果，而像如下调用则会生成一个异常

```java
Math.MultiplyExact(1_000_000_000,3);
```

### 3.5.3 数值类型之间的转换

1. 字节数少的类型转多的类型（例如byte->short)不会丢失信息，多的转少的、同字节数的可能会丢失

```java
(float)123456789=1.23456792E8
```

2. 二元运算结果优先级：double>float>long>int

### 3.5.4 强制类型转换

1. 大转小（可能丢失信息）需要使用强制类型转换
2. 舍入

```java
int nx=(int)Math.round(x);
```

3. 如果待转换的数值超出了小类型的范围，数值可能会截然不同

### 3.5.5 结合赋值和运算符

```java
x+=4;
x+=3.5;
```

> 下面一种情况会自动(int)x

### 3.5.6 自增与自减运算符

> 与C++类似

### 3.5.7 关系和boolean运算符

> 与C++类似，包括逻辑运算符的短路特性、三元运算符等

### 3.5.8 位运算符

1. &（and)	|(or)	^(xor)	-(not)
2. 用如下算法可以单独取出二进制整数中的特定位

```java
int x=(a & 0b1000) /0b1000;	//单独取出第四位
```

3. 也可以用来运算bool值，与逻辑运算符不同的是不具有短路特性
4. 位移、位掩码等概念

```java
int x=(a & (1 << 3)) >> 3;	//单独取出第四位
```

> 1 << 3 = 1000

```java
>>>会用0填充高位	>>用符号位填充高位	不存在<<<
```

5. 位运算符的右操作数要对32取模（int) 或对64取模（long)

   > int最高只有32位，long最高只有64位

### 3.5.9 括号与运算符级别

> 基本与C++相同

## 3.6 字符串

String是一个预定义类777

### 3.6.1 子串

```java
String a="hello";
a.substring(0,3);	//a的值为"hel"
```

1. 第一个参数指开始复制的字符（从0开始），*第二个参数指第一个不想被复制的字符* 
2. 字串长度为b-a

### 3.6.2 拼接

1. ”+“直接拼接
2. 非字符串与字符串拼接时会自动变为字符串，几乎任何Java对象可以转换成字符串

```java
String all=String.join("/","A","B","C","D");
// all的值为"A/B/C/D"
//join方法可以用指定分隔符把多个字符拼接在一起

String repeated="Java".repeat(3);
//repeated="JavaJavaJava"
//repeat方法是Java11引入的新特性
```

### 3.6.3 不可变字符串

1. 不可直接修改字符中的单个字符，这一操作要通过提取字串和拼接完成
2. 这样做的好处是编译器让字符串共享
3. 与类比C++的类比p46，对字符串变量重新赋值不用担心内存泄漏问题，Java会自动进行垃圾回收

### 3.6.4 检测字符串是否相等

```java
s.equals(t);	//判断字符串变量s,t是否相等
s.equalsIgnoreCase(t);	//忽略大小写判断
```

> 一定*不要用 ==* 判断字符串是否相等，只有两个相比较的字符串放在同一位置（处于共享状态）时，==才会返回true

### 3.6.5 空串与NULL串

1. 空串是一个java对象

   ```java
   if (str.lenth()==0)
   if (str.equals(""))		//以上两种方法用以判断空串
   ```

2. string变量也可以存放null,表示不与任何值关联

   ```java
   if (str == null)	//判断是否为null
   if (str != null && str.lenth()!=0)
   //判断是否既不是null,也不是空串
   //注意要先判断是不是null(不能对null调用方法)
   ```

### 3.6.6 码点与代码单元

1. length方法返回代码单元数，charAt(n)返回第n个位置的代码单元，要想得到码点需要另外的方法p48

2. char太底层了，不要使用char

3. 遍历字符串：codepoints方法，生成一个int值的"流"，每个int值代表一个码点，可以将它转换成数组

   ```java
   int[] codePoints=str.codePoints().toArray();
   ```

   用构造器把码点数组转换成字符串

   ```java
   String str=new String(codePoints,0,codePOints.length());
   ```

4. JVM不一定把字符串实现成代码序列，Java9中只包含单字节代码的字符串用byte数组实现，其他字符串用char数组

### 3.6.7 String API

> file:///C:/Users/86186/Desktop/docs/api/index.html
>
> 官方联机文档

书本介绍常用API p49

### 3.6.9 构建字符串

1. 如果字符串由多个较短的字符串拼接而成，需要多次malloc一个新的string对象再复制，耗时费力

2. ```java
   StringBuilder builder= new StringBuilder();
   //构建一个空的字符串构建器
   
   builder.append(ch);
   builder.append(str);
   //向builder中添加字符或字符串
   
   String CompletedString=builder.toString();
   //得到一个字符串
   ```

3. 常用API p54

## 3.7 输入与输出

### 3.7.1 读取输入

> API for *java.util.Scanner*	p56

```java
Scanner in=new Scanner(System.in);
//构造一个与标准输入流(System.in)关联的Scanner对象

String name=in.nextline();
//读取一整行

int age=in.nextInt();
//读取一个整数(double等类似)

String firstname=in.next();
//读取一个单词
```

> scanner类不适用于从控制台读取密码（输入需可见），用console类来读取密码到字符数组（出于安全考虑），console类的缺点是只能读取一整行，不能进行部分读取

### 3.7.2 格式化输出

1. System.out.print()

   ```java
   double x=10000/3.0;
   System.out.print(x);
   //输出3333.3333333333335	即该类型允许的最大非0数位个数
   //x=3	out:3.0
   //x=3.00	out:3.0
   ```

2. 沿用了C语言的System.out.printf()格式化输出

   ```java
   System.out.printf("%8.2f",x);
   //表示宽度为8(include point),精度(小数点后几位)为2
   //若有多余宽度，未设定标志时，默认空格
   //若宽度不够，则按刚好的宽度输出(不会牺牲精度)
   ```

3. p59给出了常用日期和时间打印格式

   ```java
   System.out.printf("%tc",new date());
   ```

4. p58给出了常用格式标志，这里重点写两个

   > 索引：$		重复：<

   ```java
   System.out.printf("%1$s %2$tB %2$te","is",date());
   //$前的数字表示输出参数表中的第几个参数
   //注意普通输出(例如%d)计算要输出哪个参数时会忽略掉%$类型
   //这样避免了多次调用date(),达到一次调用，不同格式输出
   
   System.out.printf("%tc %<tc",date());
   //<符号表示沿用上一个输出使用的参数
   ```

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210628170428982.png" alt="image-20210628170428982" style="zoom:80%;" />

### 3.7.3 文件输入与输出

书中给出的path.of方法是11开始支持的特性，这节先留着777

## 3.8 控制流程

### 3.8.1 块作用域

> 不能在嵌套的两个块中声明同一个名称的变量（不同于C++）

### 3.8.6 中断控制流程的语句

1. 带标签的break，可以跳出多个循环（类似go to）

   ```java
   read_data:
   while(){
       for(){
           while(){
               break read_data;
           }
       }
   }
   //read_data是标签，指示了while这个代码块，运行到break后直接结束这个代码块
   ```

2. continue也有带标签的用法，跳转到标签指示的代码块的首部

## 3.9 大数

> BigInteger实现任意精度的整数运算
>
> BigDemical实现任意精度的浮点数运算

```java
BigInteger a=BigInteger.valueof(100);
//valueof方法将普通数值转换为大数

BigInteger a=new BigInteger("99999······");
//对于更大的数，可以使用构造器，注意参数是字符串

//大数不能使用常规的+ -运算符，java没有为大数重载常规运算符
BigInteger c=a.add(b);	//c=a+b
d=c.multiply(b.add(BigInteger.valueof(2)));	//d=c*(b+2)
```

## 3.10 数组

### 3.10.1 声明数组

```java
int[] a=new int[100];
//int[]声明了a是一个整型数组变量，而new真正把a初始化成一个数组

int[] a={1,2,3,4,5};
//类似C++的快速初始化方法

a=new int[]{1,2,3};
//new int[]{}为一个匿名数组，这样可以直接重新初始化a
```

1. 创建数组后不能再改变大小，若要经常扩容，应使用数组列表
2. 有长度为0的数组(new int[0]或者new int[]{})

### 3.10.2 访问数组元素

> int会被初始化为0，boolean被初始化为false，对象（包括string）被初始化为null

### 3.10.3 for each循环

```java
for (int element:a){};
//这个循环可以遍历数组a中的每一个元素(从头至尾)
//element用来暂时存放a中当前遍历元素

System.out.println(Arrays.toString(a));
//更快捷的输出整个数组的方法
```

### 3.10.4 数组拷贝

```java
int[] a=new int[10];
int[] b=new int[20];
a=b;
//相同类型数组之间可以赋值，相当于两个变量引用同一个数组，一个改变了另一个也跟着改变

int[] a=Array.copyof(b,b.length);
//利用Array.copyof()方法可以把一个数组中的值拷贝到新的数组，第一个参数是要拷贝的数组，第二个参数是新数组的长度，利用这种方法可以对原数组进行扩容
```

### 3.10.5 命令行参数

> agrs为命令行启动时的自定义参数(下图中的 -b)
>
> <img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210629102913813.png" alt="image-20210629102913813" style="zoom:80%;" />

### 3.10.6 数组排序

```java
Arrays.sort(a);
//对a数组进行排序，内部用改进的快速排序实现
```

### 3.10.7 多维数组

```java
for (int[] row:a)
    for (int value:row)
//对多维数组进行for each循环
//特别注意第一行有[]，第二行没有
        
Arrays.deepToString(a);
//快速打印多维数组
```

### 3.10.8 不规则数组

> 涉及到多维数组的内部实现机制

```java
int[][] a=new int[100][];
//先分配100个连续元素，每个元素内存大小为指针大小

for (int i=0;i<100;i++){
    a[n]=new int[30];
}
//再为每一行（每一个指针）分配数组
```

## 4.1 面向对象程序设计概述

### 4.1.4 类之间的关系

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210630153726878.png" alt="image-20210630153726878" style="zoom:80%;" />

>类A当中使用了类B，其中类B是作为类A的方法参数、方法中的局部变量、或者静态方法调用。粗浅理解：B在A中的出现只是为了实现A的某个方法，并不是A的状态的一部分。

```java
public class People{
    //Book作为read方法的形参
     public void read(Book book){
        System.out.println(“读的书是”+book.getName());
    }
}
```

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210630154102117.png" alt="image-20210630154102117" style="zoom:80%;" />

> 单向关联表现为：类A当中使用了类B，其中类B是作为类A的成员变量。双向关联表现为：类A当中使用了类B作为成员变量；同时类B中也使用了类A作为成员变量。

```java
public class Son{
   //关联关系中作为成员变量的类一般会在类中赋值
    Father father = new Father();
    public void getGift(){
        System.out.println(“从”+father.getName()+”获得礼物”);
    }
}
 
public class Father{
    Son son = new Son();
    public void giveGift(){
        System.out.println(“送给”+son.getName()+“礼物”);
    }
}
```

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210630154452360.png" alt="image-20210630154452360" style="zoom:80%;" />

> 聚合关系是关联关系的一种，耦合度强于关联，他们的代码表现是相同的，仅仅是在语义上有所区别：关联关系的对象间是相互独立的，而聚合关系的对象之间存在着包容关系，他们之间是“整体-个体”的相互关系。粗浅理解：B是A的一部分，但是是可有可无的（一个人可以没有汽车），所以用一个单独的方法set来初始化

```java
public class People{
    Car car;
    House house; 
    //聚合关系中作为成员变量的类一般使用set方法赋值
     public void setCar(Car car){
        This.car = car;
    }
    public void setHouse(House house){
        This.house = house;
    }
 
    public void driver(){
        System.out.println(“车的型号：”+car.getType());
    }
    public void sleep(){
        System.out.println(“我在房子里睡觉：”+house.getAddress());
    }
}
```

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210630154710562.png" alt="image-20210630154710562" style="zoom:80%;" />

> 相比于聚合，组合是一种耦合度更强的关联关系。存在组合关系的类表示“整体-部分”的关联关系，“整体”负责“部分”的生命周期，他们之间是共生共死的；并且“部分”单独存在时没有任何意义。粗浅理解：组合相较于聚合，最大的特点就是部分的存活期小于等于整体，而且一定有（人一定有灵魂和身体），所以用构造函数直接初始化，当people对象生成的时候，他的soul和body也同时指定了

```java
Public class People{
    Soul soul;
    Body body; 
    //组合关系中的成员变量一般会在构造方法中赋值
     Public People(Soul soul, Body body){ 
        This.soul = soul;
        This.body = body;
    }
 
    Public void study(){
        System.out.println(“学习要用灵魂”+soul.getName());
    }
    Public void eat(){
        System.out.println(“吃饭用身体：”+body.getName());
    }
}
```

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210630155008975.png" alt="image-20210630155008975" style="zoom:80%;" />

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210630155417449.png" alt="image-20210630155417449" style="zoom:80%;" />

## 4.2 使用预定义类

### 4.2.1 对象与对象变量

1. 构造器即构造函数

2. ```java
   Date birthday=new Date();
   //对象变量birthday并没有实际包含一个对象，而是引用这个对象
   //new的返回值是一个引用
   ```

3. 可以显式地将变量对象设置为null，表示目前没有引用任何对象

4. <img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210630163112833.png" alt="image-20210630163112833" style="zoom:80%;" />

### 4.2.2  Java类库中的LocalDate类

1. 时间在date()内部是用距离一个固定时间点的毫秒数来表示的，这个固定时间点是UTC时间(1970.1.1 00:00:00)

2. 因为日历在各地的计算方法不一样，为了避免出现混乱，单独设计了类LocalDate

   ```java
   LocalDate.now();	
   //构造一个新对象，状态为当前日期(静态工厂方法，不能用构造器构造)
   
   LocalDate.of(2001,6,30);
   //构造一个指定日期的对象
   ```

### 4.2.3 更改器方法与访问器方法

1. 会更改调用对象的方法就是更改器方法

   ```java
   date.PlusDays();
   ```

2. 不更改只访问调用对象的方法就是访问器方法

   ```java
   date.getyear();
   ```

3. 遇到想要完成的功能，先去找接口，而不是自己写

## 4.3 用户自定义类

### 4.3.1 employee类

一个源文件只能有一个public class，并且文件名必须与public class同名

### 4.3.2 多个源文件的使用

编译源文件时当发现文件中出现其他类，会自动寻找该类的class文件，若找不到，就寻找该类的java文件进行编译，更重要的是，若该类的java文件较已有的class文件有更新，则会自动重新编译java文件

### 4.3.3 剖析Employee类

> 访问权限

private是对类的而不是对对象的，相同类的不同对象可以访问彼此的private数据

如果访问权限符缺省，则在包内public，包外private

### 4.3.5 用var声明局部变量

Java10开始，若可以从变量的初始值推导出类型，则可以用var声明局部变量

```java
var str=new string();
```

> 多使用var是一种提倡的写法
>
> 注意var只能用于方法中的局部变量。参数和字段(类的成员变量)必须声明数据类型

### 4.3.6 使用null引用

> 想清楚哪些字段可能是null，对于可以接受为null型的字段采用宽容型，不能接受的采用严格型

对于null参数：

```java
name=Objects.requireNonNullElse(n,"unknown");
//若参数n为null，就把字段name赋值成"unknown"
//宽容型

Objects.requireNonNull(n,"The name cannot be null");
name=n;
//调用这个方法时，若n为null，就会产生一个异常
//普通调用null对象的方法不是也是触发异常吗，这样做的好处在哪里？
//在于异常报告会提供这个问题的描述并准确指出问题所在的位置
//严格型
```

### 4.3.7 隐式参数与显式参数

this即为隐式参数，括号里的参数即为显式参数

### 4.3.8 封装的优点

> 如果需要返回一个可变对象的引用，首先应该对他进行克隆

```java
Employee harry=...
Date d=harry.getHireDay();
double passDays=1000;
d.setTime(d.getTime()-(long)passDays);
//通过Date类中的更改器，修改了Employee类中可变对象HireDay字段的值，而这个字段本来应该设计为只读字段，不容许外界通过任何方法直接修改，解决这一问题的办法是对可变对象HireDay进行克隆

class Employee{
    public Date getHireDay(){
        return (Date) HireDay.clone(); //为可变对象制作了一个副本
    }
}
```

### 4.3.9 基于类的权限访问

方法可以访问所属类的任何对象的私有特性

### 4.3.10 私有方法

私有方法可以作为公共方法的辅助方法，例如公共方法里需要跑一个DFS算法，那么这个DFS方法就可以设置成私有的

> 还需探究私有方法更多的使用场景777

### 4.3.11 final实例字段

可以将实例字段定义为final，这样的字段必须在初始对象时就初始化

+ 对于 *基本类型和不可变类* (类的任何方法都不能改变调用它的对象的值，比如String)来说，final修饰符意味着初始化后值不能再被改变

+ 对于 *可变类* 来说，final修饰符意味着变量中的对象引用不会再指示别的对象，但是依旧可以使用该对象的方法来改变对象的值

## 4.4 静态字段与静态方法

 ### 4.4.1 静态字段

> 静态字段=类字段，属于类自己的而非对象的字段

### 4.4.2 静态常量

```java
public static final double pi=3.1415926;
```

### 4.4.3 静态方法

> 属于类而不属于任何对象的方法，当方法不涉及到对象状态或只需要访问静态字段时，可以使用静态方法

### 4.4.4 工厂方法

> 粗浅理解：利用静态方法来实现构造器的功能

为什么不直接使用构造器？

1. 无法命名构造器。构造器的名字必须与类名相同，但是有时需要利用相同的参数用不同的方法构造对象，这时构造器就不够用了，必须使用静态工厂方法
2. 使用静态工厂方法，可以改变所构造对象的类型

### 4.4.5 main方法

## 4.5 方法参数

> 当对象变量作为参数时，传进去的实际上对象变量引用的一个副本

+ 方法不能修改基本数据类型的参数(数值或布尔型)
+ 方法可以修改对象变量的状态
+ 方法不能让一个对象参数引用一个新的对象

## 4.6 对象构造

### 4.6.1 重载

> 多个方法有相同的名字，不同的参数(比如构造器)

1. 编译器查找此调用具体匹配哪个方法的过程称作*重载解析*
2. 方法名以及参数类型称作方法的*签名*
3. 返回类型不是签名的一部分，也就是说，不能有方法名、参数相同但是返回类型不同的两个方法

### 4.6.2 默认字段初始化

局部变量必须明确初始化，而字段若没有被明确初始化，会被默认初始化(数值为0，bool型为false，对象引用为null)

### 4.6.3 无参数的构造器

1. 如果自己没有编写任何构造器，就会自动提供一个默认构造器，这个构造器将所有字段初始化为默认值

2. 一旦已经编写了任何带参数的构造器，将不会自动提供无参数的构造器，调用无参数的构造器将报错

3. 在2情况下，如果希望用户可以通过一个无参数的构造器来构造对象并使所有字段被初始化为默认值，不必手动赋值为默认值，只需提供以下代码：

   ```java
   ClassName(){}
   ```

### 4.6.4 显式字段初始化

1. 可以在类定义中为任何字段直接赋值，可以用常量赋值，也可以调用方法赋值
2. 先执行类定义中的字段赋值，再执行构造器

### 4.6.5 参数名

```java
public Employee(String n,double s){} //不好
public Employee(String aName,double aSalary){} //好，既不会屏蔽实例字段，看起来也容易懂
```

### 4.6.6 调用另一个构造器

> this的另一个语义就是调用别的构造器，这样可以复用部分构造器代码了

```java
public Employee(double s){
    //calls Employee(String,double)
    this("Employee #"+nextId,s);
    nextId++;
}
```

### 4.6.7 初始化块

> 当对字段赋值过于复杂时，可以用初始化块，这是一种不常用的方法，一般还是用构造器

```java
class Employee{
    private static int nextId;
    
    private int id;
    private double salary;
    
    //初始化块
    {
        id=nextId;
        nextId++;
    }
    
    //静态初始化块
    static
    {
        ......
        ......
    }
}
```

### 4.6.8 对象析构

由于java的自动垃圾回收机制，java不支持析构器，但如果某些对象占用了使用内存之外的资源(文件、系统资源另一个对象的句柄等)，应当将其回收或再利用。

1. 构造一个close方法，对象使用完后调用
2. 关闭钩：Runtime.addShutDownHook 虚拟机退出时运行
3. 用Cleaner类注册一个动作

## 4.7 包

### 4.7.1 包名

为了保证包名的绝对唯一，要用一个因特网域名以逆序的形式作为包名，然后对于不同的工程使用不同的子包。

### 4.7.2 类的导入

1. public class所有类都可以使用，不加修饰的默认class仅在包内可以使用，private class只能是内部类，表示只有包含此private class的类可以使用
2. import语句 如果要使用两个包里的同名类，要写完整的类名

### 4.7.3 静态导入

允许导入静态方法和静态字段

```java
import static java.lang.System.* //System是源文件名(类名)，System.*则表示System类的所有静态方法和静态字段

import static java.lang.System.out //也可以导入具体的方法或字段
```

### 4.7.4 在包中增加类

涉及到终端，没搞清楚777

### 4.7.5 包访问

777

### 4.7.6 类路径

## 4.9 文档注释

777

## 4.10 类设计技巧

1. 一定要保证数据私有

2. 一定要对数据进行初始化

3. 不要在类中使用过多的基本类型（把具有相似性质的数据封装成一个新类）

4. 不是所有字段都需要单独的访问器和更改器

5. 当一个类的职责过多，分解它，把不相干的概念和方法分解成其他类

6. 类名和方法名要能够体现它的职责

7. 优先使用不可变的类(类似subString方法是返回状态改变的新对象，而不是返回改变了状态的原来的对象)

   > 可变的类的问题在于，在多个类中共享时，如果有多个线程同时要修改类的状态，可能引发并发问题。



## 5.1 类、超类和子类

### 5.1.1 定义子类

extends关键字

### 5.1.2 覆盖方法

this是一个特殊的对象引用，而super只是一个特殊的关键字

### 5.1.3 子类构造器

1. this有两个含义，一是引用当前对象，而是调用类内其他构造器。super类似（本质不同)，当*super.字段*，*super.方法* 时，super表示父类的字段或方法，当在构造器中使用*super(agr)* 时，super表示调用父类的相应构造器
2. super()必须是构造器的第一条语句
3. 如果子类构造器没有显式调用super(agu)，将会自动调用无参数的super()，如果此时超类中没有无参数的构造器，编译器会报错

### 5.1.4 继承层次

1. 由一个公共超类派生出来的所有类的集合称为继承层次

2. 从某个特定类到其祖先的路径称为该类的继承链

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210708131659011.png" alt="image-20210708131659011" style="zoom:80%;" />

### 5.1.5 多态

1. 可以将子类对象交给父类对象变量管理，反之不行

   ```java
   Employee a=new Manager(); //ok
   Manager a=new Employee(); //wrong
   ```

2. 在一个被声明过是子类数组中存储一个父类元素会触发ArrayStoreException异常

   ```java
   Manager[] manager=new Manager[10];
   Employee[] staff=manager; //it's ok
   staff[0]=new Empolyee(arg); //不合理
   manager[0].setBonos(); //会搅乱相邻存储空间的内容
   ```

### 5.1.6 理解方法调用

1. static方法不能被重写

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210716095517250.png" alt="image-20210716095517250" style="zoom:80%;" />

2. private\static\final方法和构造器使用静态绑定，直接调用声明类(父类)中的方法

3. 为节省时间，虚拟机预先为每个类计算了一个方法表，表中包含继承方法、重写方法、新增方法。

   > 步骤：
   >
   > 1. 虚拟机获取引用(变量)实际类型的方法表
   > 2. 在方法表中查找对应签名的方法（重载解析）
   > 3. 调用这个方法

4. 重写的方法的返回类型可以是原方法的子类

   ```java
   public staff getBuddy(){}
   public Manager getBuddy(){}
   //称这两个方法有可协变的返回类型
   ```

5. 在修饰符方面，子类方法不能低于超类方法的可见性

### 5.1.7 阻止继承：final类和方法

声明为final的类不允许被继承，final方法不允许被重写，final字段初始化后不允许被修改。final类中的方法自动全部声明为final，但是字段不会被声明成fianl。

### 5.1.8 强制类型转换

1. 只能在继承层次内进行强转，否则编译根本就不会通过

2. 父类向子类(向下)强转必须转向实例实际的类型

   ```java
   SuperClass a=new SubClass();
   (SubClass)a.setSubsidy();
   //a实际上是子类manager的实例，所以可以通过强转来调用manager自己的方法
   ```

3. 若向下强转并“谎报”实际类型，会引发ClassCastException异常，若未捕获异常，则会直接终止

4. *instanceof* 可以用来判断实例是否是某类或其子类的实例

   ```java
   if (a instanceof SubClass){
       (SubClass)a.setSubsidy();
   }
   //当a为null时判断不会触发异常，会返回false
   ```

### 5.1.9 抽象类

1. 关键字abstract定义抽象类和抽象方法

   ```java
   public abstract class person{
       public abstract String getDescription();
   }
   ```

2. 抽象方法无需给出定义，类中只要有抽象方法或者有继承来的抽象方法还没有重写的就必须是抽象类

3. ~~~java
   new person(); //no，抽象类不能被直接构造
   person a=new student(); //ok,可以声明抽象类变量，并被子类赋值
   ~~~

### 5.1.10 受保护访问

+ private：仅对本类可见
+ public：完全可见
+ protected：对子类和 *本包* 可见
+ default：对本包可见

## 5.2 Object :所有类的超类

### 5.2.1 Object类型的变量

```java
var staff=new Employee[10];
object obj;
obj=staff; //ok
obj=new int[10]; //ok
//所有数组类型也是从obj扩展而来
```

### 5.2.2 equals方法

1. Object类中的equals方法仅检测引用是否相等

2. 重写equals方法要注意以下几点：

   + 检测实参是否为null
   + 检测实参是否和本实例就是同一个引用
   + 检测实参和本实例是否属于同一个类(利用getClass方法)

   通过以上检测后，进行字段值的比较阶段，需注意：

   + 实例中的字段可能为null，因此要用 *Objects.equals* 方法来判断对象类型的字段值是否相等

     ```java
     Objects.equals(a,b);
     //若a,b都为null，返回true
     //若a,b有一个为null，返回false
     //若a,b都不为null，调用a.equals(b)
     ```

3. 若父类的equals方法中已经写好了以上检测，重写子类equals方法时只需加上如下语句再判断子类字段值就可以了

   ```java
   if (!super.equals(Objects obj)) return false;
   ```

### 5.2.3 相等测试与继承

因为 （子类实例 instanceof 父类）=true，所以用instanceof代替getclass来判断是否属于同一个类的话，会使equals方法失去对称性。

两种不同的情形：

+ 如果子类有自己的相等性概念，则必须使用getclass
+ 如果相等性概念由父类定义(父类中的那些字段相等就判断这两个子类实例相等)，那么可以使用instanceof

编写一个完美的equals:

<img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210716171555703.png" alt="image-20210716171555703" style="zoom:80%;" />

```java
public boolean equals(Employee other)
//参数类型不是obj，签名不一致，相当于是定义了一个完全不相关的方法，而不是继承
//解决办法：重写之前加上@override
```

### 5.2.4 hashCode方法

这玩意干嘛用的？过了777

### 5.2.5 toString方法

1. Object中的toString方法打印 *类名@hashcode*

2. 大多数自定义类的toString都设计成 *类名[各个字段值]*

   ```java
   public String toString(){
       return getClass().getName()
           +"[salary="+salary
           +",hireDay="+hireDay
           +"]";
           
   }
   //不直接写类名，而是调用getClass().getName()的好处是子类使可以复用这个方法
   public String toString(){
       return super.toString()
           +"[bonus="+bonus
           +"]";
   }
   ```

3. toString方法非常常用，任何需要一个字符串的场合，比如字符串拼接、输出等，直接写对象名就会用toString来实现

4. toString还是非常有用的调试技巧，建议为每个类实现toString方法，并用以下日志命令来查看对象状态：

   ```java
   Logger.global.info("Current position="+position);
   ```

## 5.3 泛型数组列表

### 5.3.1 声明数组列表

```java
var staff=new ArraryList<Employee>();	//声明数组列表

//若大概已经知道要放多少元素，两种方法可以提前说明以提高性能
staff.ensureCapacity(100);	//用ensureCapacity方法
var staff=new ArraryList<Employee>(100);	//直接在构造器中声明
//注意这和new Employee[100]的区别，后者是直接分配了空间，而前者声明后并不分配空间

staff.trimToSize();	//当确定数组大小不会再变化时可以用此方法把剩余空间回收掉
```

> 另外，不同于C++中的vector，java的数组列表没有运算符重载，不能直接类似staff[1]调用，要用staff.get(1)

### 5.3.2 访问数组列表元素

```java
a.get(i)	//访问第i位
a.set(i,n)	//修改第i位
a.add(i,n)	//在第i位插入
a.remove(i)	//删除第i位
    
//先用数组列表接受数据，再倒到数组里去，就既可以灵活扩展数组，又可以方便地访问和修改数组元素
var list=new ArrayList<X>();
while (...){
    X x=...;
    list.add(x);
}
var a=new X[list.size()];
list.toArray(a);
```

###  5.3.4 类型化与原始数组列表的兼容性

过了777

## 5.4 对象包装器与自动装箱

1. 包装器：基本类型与之对应的类——Integer Long Double Float Short Byte Character boolean(前6个类型派生于公共的超类Number)

2. 包装器被声明为final，不能被继承。

3. 包装器中的值在初次设置后不允许再改变。

4. ```java
   ArrayList<int> //can't
   ArrayList<Integer> //ok
   //但是因为这样每个数值都是被包装在对象里的，所以性能较低
   ```

5. 自动装箱：

   ```java
   list.add(3); //将自动变换成
   list.add(Interger.valueOf(3)); //应该给一个包装器实例，但是直接给数值也行，会自动装箱(autoboxing)
   ```

   自动拆箱：

   ```java
   int n=list.get(i); //将自动变为
   int n=list.get(i).intValue();
   ```

   自动拆箱与装箱：

   ```java
   Integer n=3;
   n++;	//先自动拆箱进行计算，再将计算结果自动装箱
   ```

6. 注意因为包装器是对象，所以直接用==判断只是在判断是否是同一个引用而已，判断数值是否相等应该使用equals

7. 包装器是对象，可能为null，因此可能会由此触发NullPointerException异常

8. 在条件表达式中混用Integer和Double，Integer会自动拆解并重新装箱成Double

   ```java
   Integer a=1;
   Double b=1.0;
   System.out.println(true? a:b);
   //也包括三元运算符
   ```

   （这个知识点不很清楚777

9. ```java
   Integer.parseInt(s); //静态方法：字符转整型
   ```

10. 装箱和拆箱是编译器的工作，与虚拟机无关

## 5.5 参数数量可变的方法

```java
public PrintStream printf(String fmt,Objects... args){};
//...表示参数数量可变
```

编译器把参数搞成一个Objects数组赋给args

允许将数组作为最后一个参数传递给有可变参数的方法

## 5.6 枚举类

> 将字符常量与次序一一对应

奇奇怪怪，先过777

## 5.7 反射

先过了777

## 5.8 继承的设计技巧

1. 将公共操作和字段放在超类中

2. 不要使用protected

   > 这会使得任意子类和同一个包中都有权限直接访问字段。
   >
   > protected一般用于那些不提供一般用途而应在子类中重新定义的。

3. 非"is-a"关系(超类的某些字段对子类来说无意义)不要用继承

4. 如果超类的某些方法对子类来说无意义，不要用继承

5. 子类重写父类中的方法时，不要偏离此方法最初的设计预期行为(不要改成做另外一件事的方法)

6. 使用多态，而不要使用类型信息

7. 不要滥用反射

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
     >  int compare(String x,String y){ 			//接口签名
     >      return x.compare(y);  		//调用的方法
     >  }
     > }
     > ```

   + Class::staticMethod

     > 所有参数都传递到静态方法
     >
     > ```java
     > class extends interface1{
     >  Double powIt(Double x,Double y){ 			//接口签名
     >      return pow(x,y);  		//调用的方法
     >  }
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