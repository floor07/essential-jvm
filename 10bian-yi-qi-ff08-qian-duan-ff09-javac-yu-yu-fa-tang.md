## java编译器时不确定,主要有如下3类：

编译器前端：Sun的Javac，Eclipse JDT的增量式编译器ECJ

后端运行期JIT编译器： HotSpot VM的C1，C2编译器

静态提前编译器AOT编译器：GNU Complier for the java\(GCJ\)、Excelsior JET

Java编译器的运行期优化过程对应程序运行来说更重要。

前端编译器在编译器的优化过程中与程序编码密切相关。

Sun Javac的大致过程：

解析与填充符号表过程

插入式注解处理器的注解处理过程

分析与字节码生成的过程

Javac编译过程大致如下图：

### ![](/assets/complier.png)1.词法，语法解析

词法分析：将源代码的字符流转变为标记Token集合。

例如： int a=b+2 这句词法分析后，标记集合中有，int，a,=,b,+,2

由com.sun.tools.javac.parser.Scanner

语法分析：将Token序列构造成抽象语法树。

由com.sun.tools.javac.parser.Parser类实现。

抽象语法树由com.sun.tools.javac.tree.JCTree类表示。

### 2. 填充符号表

符号表由符号地址和符号信息组成的。

符号表所登记的内容将用于语句分析。

代码生成阶段，符号表，作为地址分配的依据。

由com.sun.tools.javac.comp.Enter类。

### 3. 注解处理器

### 4.语义分析与字节码生成

对源程序上下文关联性的审查：

####  4.1 标准检查

变量使用前是否已被声明，变量与赋值之间是否已经匹配等。

还有一个重要步骤：常量折叠。

例如 :int a=1+2；编译时会变成int a=3;

####  4.2 数据及控制流分析

 程序上下文逻辑校验。

 局部变量声明为final ，仅仅编译器在编译器保证。

 例如：

```
public void foo(int i){

final int var=0;

}

public void foo(int a){

int var=0;

}
```

编译后 都以一样的，不包含final

####  4.3 语法糖

由com.sun.tools.javac.comp.TransTypes类

和com.sun.tools.javac.comp.Lower类

####  4.4 生成字节码

Java语法糖

*  泛型擦除

ArrayList&lt;Integer&gt;和ArrayList&lt;String&gt;

在运行期是同一个类，ArrayLIst&lt;E&gt;

泛型的方法称为类型擦除，元数据中还保留了泛型信息可以通过反射获取。

* 自动拆箱与自动装箱，

 以Integer为例子： 自动装箱以Integer.valueOf\(\); 自动拆箱以Integer.intValue\(\);

* 循环变量

 还原成迭代器

* 变成参数

 以数组类型的参数实现。

* 条件编译

 使用常量为条件的if语句才会出现。

 虚拟机会根据条件语句的true ，false去掉不需要的语句。

