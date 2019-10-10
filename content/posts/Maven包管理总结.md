---
title: "Maven包管理总结"
date: 2019-10-03T06:49:31+08:00
draft: false
---
# Maven是java开发者的福音，从手动敲命令到Mavne的自动化构建，java从繁琐走向简单，但这样的变化并不是百利而无一害，自动化构建带来的是自主选择的减少，而且Maven对于包冲突以及传递性依赖的解决机制并不能总是让人满意。今天就来简单总结一下其中的几个困难。
*****
## Java包管理 
### 包和classpath
java的包管理就是告诉JVM第三方类库所在位置和解决冲突的过程

* 不用于java中用于解决类名冲突和访问控制的包，这里指由一堆相互关联的类经过编译所生成字节码.class集合的jar包
* classpath用来为JVM指明所能执行字节码文件所在位置
* 传递性依赖，你依赖的类又依赖了别的类，全限定类名是类的唯一标识，当多个同名类出现在一个classpath中就会造成灾难-Classpath hell

*****
## Maven包管理
### 项目结构
* 约定优于配置 
### Maven的包
* 按照约定为所有的包编号，⽅便检索 
groupId/artifactId/version，语义化版本 
* 默认的Maven中央仓库中存放着世界上所有的几乎所有的包，并按照约定的目录存放
* Maven在引入一个包后会根据这个包的`pom.xml`文件中的依赖引入所需要的所有包，直到没有可引入的包为止，这就是Maven的传递性依赖
* 项目中所需的包会自动下载到本地.m2仓库中
*****
## 包冲突的解决
### 常见的包冲突会引发的异常
    ```
    AbstractMethodError
    NoClassDefFoundError
    ClassNotFoundException
    LinkageError
    ```

### 传递性依赖的⾃动管理
* 原则：绝对不允许最终的classpath出现同名不同版本的jar包
* 依赖冲突的解决：原则：最近的胜出

### 依赖的scope
指定依赖所有效的域，隔离第三方依赖

* compile: test和生产代码中都可以使用
* test：只能在测试代码中使用
* provided: 只在编译时有效

### 手动解决包冲突的过程(以.../hcsp/resolve-package-conflict为例)
1. 首先查看项目的依赖结构，有三种方法：
    * 找到项目的maven模块，点击Dependencies
    ![模块](/images/MavenPackagemanagement/1.png)
    * 命令行mvn dependency:tree(解决冲突后的树结构)
    ![command line](/images/MavenPackagemanagement/3.png)
    * 使用插件Maven Helper
    ![插件图片](/images/MavenPackagemanagement/2.png)
2. 分析出现冲突所在的包，并去中央仓库查看对应类的源代码
    ![exception](/images/MavenPackagemanagement/4.png)

    ![analyzer](/images/MavenPackagemanagement/5.png)
    根据编译器提示抛出异常位置及插件所示冲突可知org.springframework.spring-web出现了冲突，进入中央仓库找到项目源代码所在位置
    ![](/images/MavenPackagemanagement/6.png)

    定位到所处问题的类中
    ![](/images/MavenPackagemanagement/5.1.8.png)

    ![](/images/MavenPackagemanagement/4.3.6.png)
    经过对比发现5.1.8版本中没有getJsonpFunction()方法
    ![](/images/MavenPackagemanagement/8.png)

    ![](/images/MavenPackagemanagement/7.png)
3. 手动解决冲突
    * 利用<exclusion>标签删除传递性依赖
    * 将所需要的包声名在项目pom.xml中，根据maven包管理规则使之成为被依赖的包

    ![](/images/MavenPackagemanagement/9.png)
## Maven——⾃动化构建⼯具

* 祥见Maven实战

    


    

