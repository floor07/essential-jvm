## 加载

3步：

根据全定限名获取这个类的二进制流

根据这个类的二进制流表示的静态存储结构转换为方法区运行时数据结构。

在内存中生成一个代表这个类的java.lang.Class对象，作为方法区这个类的访问入口。

---

## 验证

连接阶段的第一步。

### 1.文件格式校验

是否以OxCAFEBABE开头

主次版本号是否在虚拟机处理范围之内。

Class文件中各个部分是否有被删除或附加的信息。

### 2.元数据校验

 校验元数据是否符合Java语言规范。

### 3.字节码验证

最复杂的阶段。

通过数据流和控制流分析，确定程序语义是否合法，符合逻辑。

### 4.符号引用验证

虚拟机将符号转换为直接应用的时候进行。在解析阶段中发生。

确保解析动作正常执行，无法通过符号引用验证，将抛出一个

java.lang.IncompatibleClassChangeError异常的子类，如java.lang.IllegalAccessError,

java.lang.NoSuchFieldError,java.lang.NoSuchMethodError等。

---

## 准备

为类分配内存，并为类变量设置初始值的阶段（非用户设置的值，而是每个类型的初始值）。

reference为null，boolean 为false，其他的为0. char为‘\u0000’.

特例：如果类字段属性表中存在ConstantValue属性，将设置为该值。

例如：

public static 

final

 int value=123;

编译时Javac将会value生成ConstantValue属性，准备阶段，虚拟机就会根据ConstantValue的设置将value赋值为123.

---

## 解析

符号引用：任何字面量，起无歧义的定位到目标。

 直接引用：指向目标的指针，或相对偏移量或一个能间接定位到目标的句柄。

### 1.类和接口解析

如果当前类CurrentClass ，需要报一个从未解析过的符号引用Param，解析成一个类或接口 TargetClassOrInterface。

规则1：如果TargetClassOrInterface不是数组，虚拟机会将Param代表的全定限名传递给CurrentClass 的类加载器。加载过程，由于元数据校验，字节码校验，可能触发其他类的加载，例如父类，使用到的父接口等。如果加载过程出现异常，解析过程失败

规则2：若TargetClassOrInterface是数组，并且数组的元数据类型为对象，会先按照规则1进行加载元数据，，例如TargetClassOrInterface为\[java.lang.Integer，加载的元数据就为java.lang.Integer。之后虚拟机生成一个代表数组维度和元素的数组对象。

通过规则1或规则2，TargetClassOrInterface已经成为一个有效的类或者接口，之后进行符号引用验证，确认CurrentClass 是否有对TargetClassOrInterface的访问权限，若没有，抛出java.lang.IllegalAccessError.

### 2.字段解析

解析一个未被解析的字段符号引用，首先在class\_index中的Constant\_class\_info符号引用进行解析\(找到属性对应的类或接口\)，解析这个类或者接口（ContainFiledClassOrInterface）。

若ContainFiledClassOrInterface本身包含简单名称和字段描述都与目标字段配置的字段，返回该字段直接引用，查找结束。

若ContainFiledClassOrInterface实现了接口，从下往上查找递归搜索接口和父接口，找到包含简单名称和字段描述都与目标字段配置的字段，返回该字段直接引用。

若ContainFiledClassOrInterface不是java.lang.Object的话，从下往上递归搜索父类，找到包含简单名称和字段描述都与目标字段配置的字段，返回该字段直接引用。

若接口和父类中都有该字段，而ContainFiledClassOrInterface没有，将出现编译错误：

The XXX is ambiguous。

否则失败，NoSuchFieldException.

找到后进行权限校验，没有权限java.lang.IllegalAccessError异常。

### 3.类方法解析

解析一个未被解析的字段符号引用，首先在class\_index中进行解析\(找到方法所属的类\)，解析这个类或者接口（ContainMethodClassOrInterface）。

类方法和接口方法符号引用的常量定义是分开的，如果类方法常量定义中发现ContainMethodClass是个接口，则直接抛出java.lang.IncompatibleClassChangeError异常

通过第一步，查找类ContainMethodClass中是否包含简单名称和字段描述都与目标配置的方法，又直接返回。

否则在ContainMethodClass的父类中从下往上递归查找，包含简单名称和字段描述都与目标配置的方法，又直接返回。

否则在ContainMethodClassOrInterface实现的接口中查找，包含简单名称和字段描述都与目标配置的方法，若存在说明ContainMethodClass是一个抽象类。抛出java.lang.AbstractMethodError异常。

否则抛出java.lang.NoSuchMethodError

找到后进行权限校验，不具备访问权限抛出java.lang.IllegalAccessError.

### 4.接口方法解析

类方法和接口方法符号引用的常量定义是分开的，如果接口方法常量定义中发现ContainMethodInterface是个类，则直接抛出java.lang.IncompatibleClassChangeError异常

通过第一步，查找ContainMethodInterface中是否包含简单名称和字段描述都与目标配置的方法，又直接返回。

否则在ContainMethodInterface的父接口中从下往上递归查找，包含简单名称和字段描述都与目标配置的方法，又直接返回。

否则抛出java.lang.NoSuchMethodError

接口都是public方法，不需要进行权限校验。

---

## 初始化

用户为类字段定义的值。就是执行&lt;clinit&gt;的过程

&lt;clinit&gt;由编译器，自动收集所有static变量赋值语句和static{}组成。

收集的顺序由语句在源文件中的顺序决定。

static{}中只能访问在他之前定义的变量，在他之后定义的变量可以赋值，但是不能读取。

&lt;clinit&gt;不需要显示声明父类构造器，虚拟机保证父类的&lt;clinit&gt;在子类的&lt;clinit&gt;之前执行，

所以虚拟机第一个执行的&lt;clinit&gt;一定是java.lang.object的&lt;clinit&gt;

对于类和接口不是必须的，若没有static变量赋值语句和static{}，就可以没有&lt;clinit&gt;

接口只有static变量赋值语句，接口不需要保证父接口先执行&lt;clinit&gt;

，只有使用到父接口中的变量时，才初始化父接口。

虚拟机保证一个类的&lt;clinit&gt;只处理一次。

  


  


