---
title: "Java异常体系、日志"
date: 2019-10-14T13:20:44+08:00
draft: false
---
## 异常体系
*****
### 什么是异常
* An exception is an event that occurs during the execution of a program and disrupts the normal flow of the program's instructions.
### 异常处理在java中的工作流程
1. 当某个方法中发生了异常, 这个方法传递给运行时系统一个包含错误信息的异常对象，包括类型，程序的状态以及发生的位置.这个过程叫做抛出一个异常.
2. 当异常被抛出时，运行时系统会试图寻找能处理异常的东西. 某些可能处理异常的集合是一组有序的方法列表，从异常出现的位置按顺序被调用.这个方法的列表也被叫做调用栈. 下图展示了一个有三个方法调用的方法栈，第一个方法中有异常处理程序.

![](/images/Java Exception/exception-call stack.png)

3. 运行时系统会在调用栈中寻找一个其中含有处理异常的代码块的方法，这个代码块称作异常处理程序. 这个寻找过程从异常发生的位置沿着调用栈与方法调用过程相反.当找到适当的方法就会把当前的异常传递给他. 
如果抛出的异常对象的类型与处理程序可以处理的类型相匹配，则认为使用异常处理程序是合适的.
4. 所选的异常处理程序用于捕获异常. 如果没有找到合适的处理程序，运行时系统也就是程序将会终止

![](/images/Java Exception/exception-searching-call stack.png)
### 异常处理程序关键字
如下图

![](/images/Java Exception/exception-handling-keywords.png)

#### throw & throws

* Thethrows 用于指定方法在执行期间可能引发异常。它在调用方法时强制执行显式异常处理:

~~~

public void simpleMethod() throws Exception {
    // ...
}
~~~

* Thethrow  允许我们抛出一个异常对象来中断程序的正常流程。这是当程序不能满足给定条件时最常用的方法:

~~~

if (task.isTooComplicated()) {
    throw new TooComplicatedException("The task is too complicated");
}
~~~

### 异常继承体系

![](/images/Java Exception/Exception.png)

### 我们怎么处理异常

使用 `try-catch-finally` 语句:

```
try {
    // ...
} catch (ExceptionType1 ex) {
    // ...
} catch (ExceptionType2 ex) {
    // ...
} finally {
    // ...
}
```
or

```

try {
    // ...
} catch (FileNotFoundException | EOFException ex) {
    // ...
}
```

* 注意catch的级联所捕获的异常范围要从小到大

### Checked and Unchecked Exceptions之间的区别
1.  Checked exceptions必须在代码中用一个 `try-catch block`处理, 否则,方法必须使用throws关键字来告诉调用者这个方法可能会抛出异常. Unchecked Exceptions 不是必须要在程序中处理或者显示声明在方法签名中.
2. exception是所有checked exceptions的父类, 然而RuntimeException 是所有 unchecked exceptions的父类. 注意 RuntimeException也是Exception的子类.
3. Checked exceptions 必须在代码中处理否则会得到编译错误, if you use FileReader to read a file, it throws the FileNotFoundException and we must catch it in the try-catch block or throw it again to the caller method. Unchecked exceptions 通常由于不好的编程实践, for example, the NullPointerException when invoking a method on an object reference without making sure that it’s not null. 
4. Checked and unchecked exceptions分别称为编译时异常和运行时异常.

### Exception & Error
* 异常是表示可以从中恢复的条件的事件，而错误表示通常无法从中恢复的外部情况。

* 所有被JVM抛出的错误都是ERROR的实例或者其子类的. 几个常见错误:

    * OutOfMemoryError – thrown when the JVM cannot allocate more objects because it is out memory and the garbage collector was unable to make more available.
    * StackOverflowError – occurs when the stack space for a thread has run out. This is typically because an application recurses too deeply.
    * ExceptionInInitializerError – signals that an unexpected exception occurred during the evaluation of a static initializer.
    * NoClassDefFoundError – is thrown when the classloader tries to load the definition of a class and couldn’t find it, usually because the required class files were not found in the classpath.
    * UnsupportedClassVersionError – occurs when the JVM attempts to read a class file and determines that the version in the file is not supported, normally because the file was generated with a newer version of Java
### Chained Exception
* 异常链特性允许您将另一个异常与异常关联起来。第二个异常描述了第一个异常的原因。
* [一个异常链的实例](https://www.javaguides.net/2018/08/java-chained-exceptions-example.html)

### 自定义一个异常

Here are the steps that create a custom exception:

1. Create a new class whose name should end with an Exception, like the ClassNameException. This is a convention to differentiate an exception class from regular ones.
2. Make the class extends one of the exceptions that are subtypes of the  java.lang.Exception class. Generally, a custom exception class always extends directly from the Exception class.
3. Create a constructor with a String parameter, which is the detail message of the exception. In this constructor, simply call the super constructor and pass the message. In Java, there are two types of exceptions — checked and unchecked exceptions.

A simple example of a custom exception is shown below.
```

public class ResourceNotFoundException extends Exception {
    private static final long serialVersionUID = 1L;
    public ResourceNotFoundException(Object resourId) {
        super(resourId != null ? resourId.toString() : null);
    }
}
```
### Try-With-Resources Statement

In Java, the try-with-resourcesstatement is a try statement that declares one or more resources. The resource is as an object that must be closed after finishing the program. The try-with-resources statement ensures that each resource is closed at the end of the statement execution.

eg.
```
public class BufferedReaderExample {
    public static void main(String[] args) {
        try (FileReader fr = new FileReader("C:/workspace/java-io-guide/sample.txt"); BufferedReader br = new BufferedReader(fr);) {
            String sCurrentLine;
            while ((sCurrentLine = br.readLine()) != null) {
                System.out.println(sCurrentLine);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### Stacktrace
* 异常栈轨迹提供所调用的类和方法的名称，从应用程序开始到发生异常为止.

* 这是一个非常有用的调试工具，因为它使我们能够准确地确定异常在应用程序中抛出的位置以及导致异常的原始原因。

* 当main()方法抛出异常时，Java运行时将终止程序并打印异常消息和栈轨迹到控制台.

### Can You Throw any Exception Inside a Lambda Expression’s Body?

* When using a standard functional interface already provided by Java, you can only throw unchecked exceptions because standard functional interfaces do not have a “throws” clause in its method signatures:

```
List<Integer> integers = Arrays.asList(3, 9, 7, 0, 10, 20);
integers.forEach(i -> {
    if (i == 0) {
        throw new IllegalArgumentException("Zero not allowed");
    }
    System.out.println(Math.PI / i);
});
```
* However, if you are using a custom functional interface, throwing checked exceptions is possible:

### 使用异常机制的一些规则

#### 抛出原则
* 能⽤if/else处理的，不要使⽤异常
* 尽早抛出异常
* 异常要准确、带有详细信息
* 抛出异常也⽐悄悄地执⾏错误的逻辑强的多

#### 异常的处理原则

* 本⽅法是否有责任处理这个异常？
不要处理不归⾃⼰管的异常
* 本⽅法是否有能⼒处理这个异常？
 如果⾃⼰⽆法处理，就抛出
* 如⾮万分必要，不要忽略异常







