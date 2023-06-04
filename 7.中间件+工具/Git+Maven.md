# Git

## 一、概述

Git 是一个免费的、开源的 **分布式版本控制系统 **，可以快速高效地处理从小型到大型的各种项目。

- 什么是版本控制？	

  答：是一种记录文件内容变化的系统，从而让用户能够查看历史版本，方便版本切换。

- 为什么需要版本控制?    

  答：个人开发过渡到团队协作。

**两种版本控制工具：**

1. 集中式版本控制工具

   SVN、CVS 、VSS

2.  分布式版本控制工具

​		**Git**、Mercurial、Bazaar、Darcs……

**Git工作机制：**

工作区(代码文件目录)  -------git add------->  暂存区(临时存储) 

​									   ----git commit----->  本地库(历史版本v1、v2……)

​									   ---------push--------->  远程库(如：GitHub、Gitee)

**Git** **和代码托管中心**：

局域网（私人，不开源）： GitLab

互联网： GitHub、Gitee 码云

**安装Git**：

官网



## 二、常用命令

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207091707631.png" alt="image-20220709165841315" style="zoom: 67%;"/>

1. 设置用户签名。
2. 初始化本地库 / 查看本地库状态 
3. 添加暂存区
4. 提交本地库
5. 查看历史版本 / 版本穿梭



## 三、Git分支操作

同时并行推进多个功能开发，提高开发效率。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207091736825.png" alt="image-20220709173605763" style="zoom:50%;" />

各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败的分支删除重新开始即可。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207091735676.png" alt="image-20220709173519610" style="zoom:80%;" />

1. 查看分支
2. 创建分支
3. 切换分支（如：切换到hot-fix分支进行修复bug）
4. 合并分支 （需要在master分支上进行操作，把需要合并的分支名输入）



## 四、Github操作

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207091828837.png" alt="image-20220709182803746" style="zoom:80%;" />

1. 创建远程仓库别名
2. 推送(push)本地分支到远程仓库。
3. 拉取(pull)远程仓库到本地库。【拉取后会自动提交，更新版本。版本信息在GitHub获取】
4. 克隆远程仓库到本地。（自动初始化）



### 团队协作

管理员，用户1，用户2。

管理员创建了一个GitHub项目。

管理员：用push更新本地库项目到GitHub。用pull拉取项目更新到本地库。可以邀请成员加入该项目。

用户：用clone该项目到本地。用push更新项目（前提需要是该项目的成员/创始人）。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207092016468.png" alt="image-20220709201653246" style="zoom:67%;" />

### 跨团队协助

其他团队需要协助该Github项目时：

先fork一份该Github项目，这样在其他管理员GitHub网站也会有一个该项目。

修改开发完成后，通过pull request进行更新。该项目管理员可通过查看所有的pull request请求，同意合并。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207092017012.png" alt="image-20220709201714905" style="zoom: 67%;" />



## 五、idea集成

### idea集成Git

准备工作：

1. 配置Git忽略文件
2. idea中配置Git.exe程序，集成到idea开发工具中。
3. 初始化本地库（VCS）

操作：

1. 添加暂存区
2. 提交本地库
3. 切换版本
4. 创建分支 / 切换分支
5. 合并分支 / 冲突合并 

### idea集成Github

1. 设置**Github账号**（还可以通过token）
2. **分享工程**到Github，无需到Github手动创建仓库。
3. **push**项目到Github。
4. 从Github上**pull**项目更新到本地idea.
5. **克隆**到本地idea。



## 六、国内平台Gitee

## 七、自建代码平台GitLab



**常用命令：**

git status																											状态查看，红色表示未加入缓冲区

① git add *.md	

② git commit -m "版本信息"

git remote -v 																										查看所有远程地址别名

git remote add study_note https://github.com/LifeSum12/study_note	添加GitHub路径

③ git push study_note master																		master表示当前分支







# Maven

在线文档

http://heavy_code_industry.gitee.io/code_heavy_industry/pro002-maven/

## 概述

**依赖管理工具：**

​	管理规模庞大的 jar 包，需要专门工具。

**构建管理工具：**

​	脱离 IDE 环境执行构建操作，需要专门工具。

**工作机制：**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207101652140.png" alt="image-20220710165217055" style="zoom:67%;" />

## 准备工作

1. 下载maven核心程序，解压即可，无需安装。
2. 配置本地仓库位置（默认自动配置到C盘）
3. 【可选】配置镜像仓库，使用阿里云的镜像仓库。
4. 【可选】配置Maven工程 JDK 版本为1.8，默认是1.5

5. 配置环境变量
   - 新增环境变量 MAVEN_HOME，值为maven文件目录。
   - 在path变量中添加 bin目录。



## 操作

**三个目录：**

Maven核心程序、Maven本地仓库、本地工作空间

**打包方式：**

java工程会打包成jar（默认）、web工程会打包成war、父工程打包为pom工程

**创建maven工程：**

- 工程创建好后，存放在“工作空间”

- 使用maven，实际上在操作pom.xml文件 （Project Object Model）

### mvn构建命令

**1、要求**

运行 Maven 中和构建操作相关的命令时，必须进入到 pom.xml 所在的目录。

**2、清理操作**

mvn clean

效果：删除 target 目录

**3、编译操作**

主程序编译：mvn compile

测试程序编译：mvn test-compile

主体程序编译结果存放的目录：target/classes

测试程序编译结果存放的目录：target/test-classes

**4、测试操作**

mvn test

测试的报告存放的目录：target/surefire-reports

**5、打包操作**

mvn package

打包的结果——jar 包，存放的目录：target

**6、安装操作**

mvn install

将本地构建过程中生成的 jar 包存入 Maven 本地仓库。这个 jar 包在 Maven 仓库中的路径是根据它的坐标生成的。



### 依赖

 **mvnrepository.com 搜索需要的jar包，网站会自动提供dependency配置代码。**

**代码：**

工程的 pom.xml 中，找到 dependencies 标签，在 dependencies 标签中做如下配置。

```xml
<dependency>
	<groupId>com.atguigu.maven</groupId>
	<artifactId>pro01-maven-java</artifactId>
	<version>1.0-SNAPSHOT</version>
</dependency>
```

打包后，如果是web项目通过查看 war 包内的结构，可以看到依赖的 Java 工程会变成 Web 工程的 WEB-INF/lib 目录下的 jar 包。

**依赖范围：**

标签：scope

标签的可选值：**compile**(默认)/**test**/**provided**/system/runtime/**import**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207102051453.png" alt="image-20220710205117330" style="zoom:80%;" />

**test：测试过程中使用的 jar 包，以 test 范围依赖进来。比如 junit。**

**provided已提供：表示该包tomcat已经提供了，不需要再部署到服务器中。比如 servlet-api、jsp-api**。

- main/test目录有效，指在该目录下是否可以使用 该类。
- 开发过程有效，指在idea开发工具时是否会有 该类 提示信息。
- 部署到服务器有效，指在web工程打包到服务器时，是否会把 该类 打包为jar过去（放在WEB-INF/ lib下）



**依赖传递性**

在 A 依赖 B，B 依赖 C 的前提下，C 是否能够传递到 A，取决于 B 依赖 C 时使用的依赖范围。

- B 依赖 C 时使用 compile 范围：可以传递
- B 依赖 C 时使用 test 或 provided 范围：不能传递，所以需要这样的 jar 包时，就必须在需要的地方明确配置依赖才可以。



**依赖的排除**

如图，当A依赖B和C时，有同一个包可能产生冲突（或者不想要），A可以排除掉X-1.0.jar，但B工程仍有(因为是B的依赖)，仅仅是在A中不能使用。

<img src="http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img027.2faff879.png" alt="./images" style="zoom:67%;" />

配置方式：标签exclude

```xml
<dependency>
	<groupId>com.atguigu.maven</groupId>
	<artifactId>pro01-maven-java</artifactId>
	<version>1.0-SNAPSHOT</version>
	<scope>compile</scope>
	<!-- 使用excludes标签配置依赖的排除	-->
	<exclusions>
		<!-- 在exclude标签中配置一个具体的排除 -->
		<exclusion>
			<!-- 指定要排除的依赖的坐标（不需要写version） -->
			<groupId>commons-logging</groupId>
			<artifactId>commons-logging</artifactId>
		</exclusion>
	</exclusions>
</dependency>
```

### 继承

Maven工程之间，A 工程继承 B 工程。

本质上是 A 工程的 pom.xml 中的配置继承了 B 工程中 pom.xml 的配置。依赖中版本号没有写，就是因为继承了父工程（父工程也没写，那就是爷爷工程写了）

**作用：**在父工程中统一管理项目中的依赖信息，具体来说是管理依赖信息的版本。一处修改处处生效。

**配置方式：**

1. 创建一个父工程，打包方式为pom

2. 在父工程目录下，创建子工程

   - 创建成功后，父工程pom.xml会出现 module标签，代表它的子工程

    ```xml
    <modules>  
    	<module>pro04-maven-module</module>
    	<module>pro05-maven-module</module>
    	<module>pro06-maven-module</module>
    </modules>
    ```

   - 创建成功后，父工程pom.xml会出现 parent标签，代表它的父工程

   ```xml
   <parent>
   <!-- 父工程的坐标 -->
   <groupId>com.atguigu.maven</groupId>
   <artifactId>pro03-maven-parent</artifactId>
   <version>1.0-SNAPSHOT</version>
   </parent>
   ```

3. 在父工程中配置依赖的统一管理，使用dependencyManagement标签。

   ```xml
   <dependencyManagement>
       <dependencies>
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-core</artifactId>
               <version>4.0.0.RELEASE</version>
           </dependency>
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-beans</artifactId>
               <version>4.0.0.RELEASE</version>
           </dependency>
       </dependencies>
   </dependencyManagement>
   ```

4. 子过程仍然需要配置，在dependencies标签。但可以省略版本号，版本号不一致将会遵循字工程。

   ```xml
   <dependencies>
   	<dependency>
   		<groupId>org.springframework</groupId>
   		<artifactId>spring-core</artifactId>
   	</dependency>
   	<dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-beans</artifactId>
   	</dependency>
   </dependencies>
   ```

**自定义属性标签：**

声明，在properties标签下。

```xml
<properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	
	<!-- 自定义标签名，维护Spring版本数据 -->
	<atguigu.spring.version>4.3.6.RELEASE</atguigu.spring.version>
</properties>
```

使用，用特殊符号。

```xml
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-core</artifactId>
				<version>${atguigu.spring.version}</version>
			</dependency>
```



## IDEA环境下使用

1.在idea配置maven。maven安装目录，配置文件及仓库位置。（idea默认有它自带的jdk和maven），自己配置可以使用新版的。

2.新建一个maven工程。maven工程下面建子工程（右键新建module即可），xml配置文件idea会自动添加需要的代码。

3.命令行执行

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207111537359.png" alt="image-20220711153718265" style="zoom: 67%;" />

4.项目导入、模块导入



## 其他概念

### 生命周期

| 生命周期名称 | 作用         | 各个环节                                                     |
| ------------ | ------------ | ------------------------------------------------------------ |
| Clean        | 清理操作相关 | pre-clean clean post-clean                                   |
| Site         | 生成站点相关 | pre-site site post-site deploy-site                          |
| Default      | 主要构建过程 | validate generate-sources process-sources generate-resources process-resources 复制并处理资源文件，至目标目录，准备打包。 compile 编译项目 main 目录下的源代码。 process-classes generate-test-sources process-test-sources generate-test-resources process-test-resources 复制并处理资源文件，至目标测试目录。 test-compile 编译测试源代码。 process-test-classes test 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。 prepare-package package 接受编译好的代码，打包成可发布的格式，如JAR。 pre-integration-test integration-test post-integration-test verify install将包安装至本地仓库，以让其它项目依赖。 deploy将最终的包复制到远程的仓库，以让其它开发人员共享；或者部署到服务器上运行（需借助插件，例如：cargo）。 |

- 三个生命周期
- 同一个生命周期的，执行某个步骤，都会按照流程从上往下执行。

### 插件和目标

①插件（jar包）

Maven 的核心程序仅仅负责宏观调度，不做具体工作。具体工作都是由 Maven 插件完成的。例如：编译就是由 maven-compiler-plugin-3.1.jar 插件来执行的。

②目标

一个插件可以对应多个目标，而每一个目标都和生命周期中的某一个环节对应。

Default 生命周期中有 compile 和 test-compile 两个和编译相关的环节，这两个环节对应 compile 和 test-compile 两个目标，而这两个目标都是由 maven-compiler-plugin-3.1.jar 插件来执行的。

### 仓库 

**需要jar包时，本地仓库没有时候，会去访问远程仓库。**

- 本地仓库：在当前电脑上，为电脑上所有 Maven 工程服务

- 远程仓库：需要联网，按以下优先级访问

  - 局域网：我们自己搭建的 Maven 私服，例如使用 Nexus 技术。

  - Internet

    - 中央仓库（官方）
    - 镜像仓库：内容和中央仓库保持一致，但是能够分担中央仓库的负载，同时让用户能够就近访问提高下载速度，例如：Nexus aliyun



专门搜索 Maven 依赖信息的网站：https://mvnrepository.com/