## 方法调用

方法调用不是方法执行，仅仅确定被调用的方法的版本（即调用的是哪一个方法）

解析，分配，是两种确定方法调用的方式，并不互斥。

java虚拟机5条方法调用指令：

 invokestatic: 调用静态方法

invokespecial：调用实例构造方法，私有方法，父类方法。

invokevirtual： 调用所有的虚方法

invokeinterface：调用接口方法，会在运行时再确定一个实现此接口的对象。

invokedynamic：由用户所设定的引导方法决定。

### 1.解析

 在类加载解析阶段，将一部分符号引用转换为直接引用，

 这阶段可以 处理“编译期可知，运行期不可变”的方法、

 Java中有：静态方法，私有方法，实例构造器，父类的方法。（非虚方法）

 除上述方法，和final方法外的方法称为虚方法。

### 2.分配

 即可动态也可静态，根据宗量书划分：单分派，多分派。

#### 静态分配：

所有依赖静态类型（类）来定位方法执行版本的称为静态分配。

典型应用重载。

重载优先级说明：

char--&gt;int---&gt;long---&gt;float---&gt;double 进行转换

转换不成，进行自动装箱

不行，转换装箱类实现的接口

不行，装箱类的父类，从下往上开始搜索，越接近上层优先级越低。

最后，可见变长参数的重载优先级是最低的。

举例说明：

**1.方法静态分配演示**

```java
public class StaticDispatch {
    static abstract class Human{

    }
    static class Man extends Human{

    }
    static class Woman extends Human{

    }
    public static void sayHello(Human human){
        System.out.println("hello Human");
    }
    public static void sayHello(Man man){
        System.out.println("hello Human");
    }
    public  static void sayHello(Woman woman){
        System.out.println("hello woman");
    }
    public static void main(String[] args){
        Human man=new Man();
        Human woman=new Woman();
        StaticDispatch.sayHello(man);
        StaticDispatch.sayHello(woman);
    }
}
```

结果都是：

hello Human

hello Human

**2.重载方法匹配优先级**

```java
public class Overload {
    // 优先级 从上往下，越来越低
    public static void sayHello(char arg){
        System.out.println("hello char");
    }
    public static void sayHello(int arg){
        System.out.println("hello int");
    }
    public static void sayHello(long arg){
        System.out.println("hello long");
    }
    public static void sayHello(Character arg){
        System.out.println("hello Character");
    }
    public static void sayHello(Serializable arg){
        System.out.println("hello Serializable");
    }
    public static void sayHello(Object arg){
        System.out.println("hello Object");
    }
    public static void sayHello(char...arg){
        System.out.println("hello arg...");
    }
    public static void main(String[] args) {
        Overload.sayHello('a');
    }
}
```

#### 动态分配：

与重写有很密切的关系。

字节码对应的指令：

invokevirtual步骤介绍

1）找到操作数栈顶第一个元素所指向的对象的实际类型，称为C。

2）如果C中找到与常量中的描述符和简单名称都相符的方法，进行访问权限验证，如果通过则返回这个方法的直接引用，查找过程结束。权限校验不通过，则抛出异常java.lang.IllegalAccessError.

3\)C类中未找到，安装继承关系从下到上依次对C的各个父类进行第二步的搜索和验证过程。

4）始终未找到，则抛出java.lang.AbstractMethodError异常。

例子：

```
public class DynamicDispatch {
    static abstract class Human{
        protected abstract void sayHello();
    }
    static class Man extends Human{
        @Override
        protected void sayHello() {
            System.out.println("Man sayHello");
        }
    }
    static class Woman extends Human{
        @Override
        protected void sayHello() {
            System.out.println("Woman sayHello");
        }
    }
    public static void main(String[] args) {
        Human man=new Man();
        Human woman=new Woman();
       // 仅有一个综量，即方法接收者，也就是方法调用者，这里指 man，woman的实际类型
     man.sayHello();
        woman.sayHello();
        man=new Woman();
        man.sayHello();
    }
}
```

#### 单分派与多分派

方法的接收者与方法的参数统称为方法的综量。

Java语言，静态分派属于多分派，动态单分派。

例子：

```
public class Dispatch {
    static class QQ{

    }
    static class _360{

    }
    public static class Father{
        public void hardChoice(QQ arg){
            System.out.println("father choose qq");
        }
        public void hardChoice(_360 arg){
            System.out.println("father choose 360");
        }
    }
    public static class Son extends Father{
        @Override
        public void hardChoice(QQ arg) {
            System.out.println("son choose qq");
        }

        @Override
        public void hardChoice(_360 arg) {
            System.out.println("son choose 360");
        }
    }

    public static void main(String[] args) {
        Father father=new Father();
        Father son=new Son();
        // 上述father和son 编译时都是Father，
        // 下边会依据参数类型进行选择
        //举例说明 father.hardChoice(new _360()),
        // 其在【静态分配】，依据【Father，和参数 _360()】两个综量（多综量） ，确定了hardChoice(new _360())
        // 方法簇即包含Father类的hardChoice(new _360())和Son类的hardChoice(new _360())
        //之后再【动态分配】阶段，影响分配的仅有father（【方法接收者】）的实际类型，单个综量
        //在这里father的实际类型是Father，所以调用的是Father类的hardChoice(new _360())
        father.hardChoice(new _360());
        //QQ类型的类似
        son.hardChoice(new QQ());
    }
}
```

#### 虚拟机动态分配的实现：

动态分配是非常平凡的操作，虚拟机基于性能考虑，大部分不会进行频繁的搜素，一般采取“稳定优化”手段就是，就是建立虚方法表 vtable，在invokeinterface执行时也会用到接口方法表——Inteface Mehod Table 简称itable。其结构如下：

![](C:/Users/Administrator.WIN-C9942BDA1C2/AppData/Local/YNote/data/floor07@126.com/1843010f934049c9bf3796054692af5c/clipboard.png)![](/assets/clipboard.png)

除了itable外，由于内联缓存和“类型继承分析”。

3.JDK1.7以后动态语言支持

 动态语言2个特点：

1. 类型检查的主体过程在运行期

2. 变量无类型而变量的值才有类型。

 java.lang.invoke包，提供了动态确定目标方法的机制MethodHandle。

例子：

```
package com.jvm.learning.eighth;
import java.lang.invoke.MethodHandle;
import java.lang.invoke.MethodType;
import static java.lang.invoke.MethodHandles.lookup;

public class MethodHandleTest {
    static class classA{
        public void println(String s){
            System.out.println("I'm in");
            System.out.println(s);
        }
    }

    public static void main(String[] args) throws Throwable {
        Object obj=System.currentTimeMillis()%2==0?System.out:new classA();
        getPrintlnMH(obj).invokeExact("myhandle test");
    }

    private static MethodHandle getPrintlnMH(Object receiver) throws NoSuchMethodException, IllegalAccessException {
        /**
         * MethodType代表方法的类型，包含方法的返回值（第一个参数），之后是按顺序的方法接收的参数
         * */
        MethodType mt= MethodType.methodType(void.class,String.class);
        /**
         * lookup方法来自于MethodHandles.Lookup
         * 用于在指定类（第一个参数），指定方法名称（第二个参数），指定方法类型（第三参数）查找
         * 符合访问权限的方法句柄
         * **/
        /**
         * 因为这里调用的是一个虚方法，
         * 按照java语言的规范，第一个参数是隐式的代表该该方法的接收者，就是this，这里由bindTo方法进行处理。
         * **/
        return lookup().findVirtual(receiver.getClass(),"println",mt).bindTo(receiver);
    }
}
```

## MethodHandles.Lookup中3个方法说明：

findStatic，findVirtual，findSpecial三个方法。

对应字节码分别是：

invokestatic，invokevirtual&invokeinterface，invokespecial

MethodHandle与Reflection的区别：

MethodHandle服务于所有java虚拟机上的语言，Reflection仅仅服务于java语言。

Reflection在模拟Java代码层次的调用，而MethodHandle在模拟字节码层次的方法调用。

Reflection是重量级，而MethodHandle是轻量级。

MethodHandle有不完善的内联优化，Reflection完全没有。

invokedynamic与MethodHandle

目的一致，均为了支持java虚拟机上的所有语言的动态类型。

MethodHandle使用java的API

invokedynamic使用字节码和Class中其他属性，常量来完成

### 4. 基于栈的字节码执行解析引擎

![](/assets/bytecodeparse.png)

基于栈的指令集的优点是可移植。代码相对紧凑，编译器实现简单。

缺点是执行速度慢。

