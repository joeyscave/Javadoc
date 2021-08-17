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
4.  位移、位掩码等概念

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

