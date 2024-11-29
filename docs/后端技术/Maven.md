# Maven

## pom.xml文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- 模型版本 -->
    <modelVersion>4.0.0</modelVersion>
    
    <!-- 组织ID -->
    <groupId>com.example</groupId>
    
    <!-- 项目ID -->
    <artifactId>my-project</artifactId>
    
    <!-- 版本号 -->
    <version>1.0.0-SNAPSHOT</version>
    
    <!-- 打包类型 -->
    <packaging>jar</packaging>
</project>
```

### Packaging定义规则

**packaging 属性为 jar（默认值），代表普通的Java工程，打包以后是.jar结尾的文件。**

**packaging 属性为 war，代表Java的web工程，打包以后.war结尾的文件。**

**packaging 属性为 pom，代表不会打包，用来做继承的父工程。**



### 依赖管理和添加

```xml
<dependencies>
    <!-- 引入具体的依赖包 -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
        <!--
            生效范围
            - compile ：main目录 test目录  打包打包 [默认]
            - provided：main目录 test目录  Servlet
            - runtime： 打包运行           MySQL
            - test:    test目录           junit
         -->
        <scope>runtime</scope>
    </dependency>

</dependencies>
```

### 依赖添加失败和解决方案

在使用 Maven 构建项目时，可能会发生依赖项下载错误的情况，主要原因有以下几种：

1. 下载依赖时出现网络故障或仓库服务器宕机等原因，导致无法连接至 Maven 仓库，从而无法下载依赖。
2. 依赖项的版本号或配置文件中的版本号错误，或者依赖项没有正确定义，导致 Maven 下载的依赖项与实际需要的不一致，从而引发错误。
3. 本地 Maven 仓库或缓存被污染或损坏，导致 Maven 无法正确地使用现有的依赖项，并且也无法重新下载！

解决方案：

1. 检查网络连接和 Maven 仓库服务器状态。
2. 确保依赖项的版本号与项目对应的版本号匹配，并检查 POM 文件中的依赖项是否正确。
3. 清除本地 Maven 仓库缓存（lastUpdated 文件），因为只要存在lastupdated缓存文件，刷新也不会重新下载。本地仓库中，根据依赖的gav属性依次向下查找文件夹，最终删除内部的文件，刷新重新下载即可！

    例如： pom.xml依赖

```XML
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid</artifactId>
  <version>1.2.8</version>
</dependency>
```

文件：
![](..\images\maven错误处理.png)

脚本使用：

根据一下代码创建bat程序

```
@echo off
rem 这里写你的仓库路径
set REPOSITORY_PATH=D:\repository
rem 正在搜索...
for /f "delims=" %%i in ('dir /b /s "%REPOSITORY_PATH%\*lastUpdated*"') do (
    del /s /q %%i
)
rem 搜索完毕
pause
```




```XML
使用记事本打开
set REPOSITORY_PATH=D:\repository  改成你本地仓库地址即可！
点击运行脚本，即可自动清理本地错误缓存文件！！
```



### Maven的继承和聚合

#### 继承

**通过子模块继承父工程的方法，继承配置达到统一项目模块配置的效果**

- 子项目通过`<parent>` 标签指定父工程
- 父工程修改打包方式为pom `<packaging>pom</packaging>` 

父工程统一声明依赖版本 并通过`<properties>` 标签统一声明版本

```xml
<properties>
        <springframework.version>4.0.0.RELEASE</springframework.version>
</properties>
```

```xml
<!-- 使用dependencyManagement标签配置对依赖的管理 -->
<!-- 被管理的依赖并没有真正被引入到工程 -->
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>${springframework.version}</version>
    </dependency>
  </dependencies>
</dependencyManagement>
```

子工程引用版本

```xml
<!-- 子工程引用父工程中的依赖信息时，可以把版本号去掉。  -->
<!-- 把版本号去掉就表示子工程中这个依赖的版本由父工程决定。 -->
<!-- 具体来说是由父工程的dependencyManagement来决定。 -->
<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
  </dependency>
</dependencies>
```

#### 聚合

**聚合作用**

1. 统一管理子项目构建：通过聚合，可以将多个子项目组织在一起，方便管理和维护。
2. 优化构建顺序：通过聚合，可以对多个项目进行顺序控制，避免出现构建依赖混乱导致构建失败的情况。

**通过`<module>` 指定聚合模块**

```xml
<project>
  <groupId>com.example</groupId>
  <artifactId>parent-project</artifactId>
  <packaging>pom</packaging>
  <version>1.0.0</version>
  <modules>
    <module>child-project1</module>
    <module>child-project2</module>
  </modules>
</project>
```

**聚合演示**

通过触发父工程构建命令、引发所有子模块构建

![Maven聚合](..\images\Maven聚合.png)