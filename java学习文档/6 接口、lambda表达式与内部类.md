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

6. 240页子类那段话属实没看懂777

   <img src="C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210718233050973.png" alt="image-20210718233050973" style="zoom:80%;" />

## 6.2 lamda表达式

