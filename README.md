# StartFromZero-Maven
##简介
Maven 多模块项目管理
1 安装maven（maven实战）
安装maven 并配置环境变量
配置本地仓库路径，修改conf/setting.xml文件的localRepository节点（全局的）
或者再本地仓库目录中新建setting.xml文件，localRepository节点设置为当前路径
以上配置可通过eclipse可视化界面操作

##pom.xml  
pom.xml是maven项目的核心。POM(Project Object Model，项目对象模型)定义了项目的基本信息，用于描述项目如何构建，生命项目依赖等等  
###结构介绍
- project是pom.xml文件的根元素，声明了一些POM相关的命名空间和xsd元素  
	`<project xmlns="http://maven.apache.org/POM/4.0.0"`  
	`　　　　　　　　xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"`  
  	`　　　　　　　　xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"></project>`  
- properties 定义常量，供下文通过${xxx}引用 如拥有多个依赖spring-core-4.17,spring-aspect-4.17等，如要升级依赖，就需要修改很多地方。可以使用在properties下定义常量`<springVersion>4.17</springVersion>`, 下文通过${springVersion}引用
- GAV 定义一个项目的基本坐标(必须)
	- groupId 定义当前maven项目隶属的实际项目，采用反向域名的表示方式与Java包名的表示方式类似，com.google.myapp  
	- artifactId 定义实际项目中的一个maven项目(模块)，通常使用项目名称-模块名称  
	- version 版本号  

- packaging 定义项目的打包方式(可选)
	- jar 默认
	- war
- classifier 定义构件输出的附属构件，如javadoc和javasource
- dependencies 项目依赖
	- dependency 具体依赖GAV信息
		- groupId
		- artifactId
		- version
		- scope 管理依赖部署
			- compile 默认值，编译 测试 运行 打包可用
			- provided 编译 测试可用
			- runtime 测试运行时可用
			- test 编译运行测试代码可用
			- system 类似于provided，需要通过systemPath元素显式提供包含依赖的jar，maven不会在repository中查找他
		- type 依赖的类型，默认为jar，对应于项目坐标定义的packaging
		- optional 标记依赖是否可选
		- exclusions 用来排除传递性依赖

##依赖
###配置依赖
`<dependencies>`  
　　`<dependency>`  
　　　　`<groupId></groupId>`  
　　　　`<artifactId></artifactId>`  
　　　　`<version></version>`  
　　　　`<scope></scope> <!--默认compile-->`  
　　　　`<optional></optional> <!--标记依赖是否可选，依赖不会被传递-->`  
　　　　`<exclusions> <!--排除传递性依赖-->`  
　　　　　　`<exclusion>  <!--只需要groupId和artifactId就可定位要排除的依赖-->`  
　　　　　　　　`<groupId></groupId>`  
　　　　　　　　`<artifactId></artifactId>`  
　　　　　　`</exclusion>`  
　　　　`</exclusions>`  
　　`</dependency>`  
`</dependencies>` 

###传递性依赖
假设A依赖于B，B依赖于C，则A对于C是传递性依赖。我们称A对于B是第一直接依赖，B对于C是第二直接依赖  

|第一直接依赖/第二直接依赖|compile|test|provided|runtime|
|:-:|:-:|:-:|:-:|:-:|
|compile|compile|-|-|runtime|
|test|test|-|-|test|
|provided|provided|-|provided|provided|
|runtime|runtime|-|-|runtime|
针对传递性依赖可能引起的冲突，如传递性依赖中包含两个相同的依赖，或同一类型依赖的不同版本，maven坚持两项基本原则：  
1. 最短路径优先
2. 最先生命优先


##仓库
在某个位置统一存储所有Maven项目共享的构件，这个位置就是仓库  
###仓库的分类
对于Maven来说，仓库只有两类：本地仓库和远程仓库。Maven首先在本地仓库查找构件，如果找不到，再到远程仓库查找。找到后则下载到本地仓库，找不到就会报错  

- 仓库
 	- 本地仓库
 	- 远程仓库
	    - 中央仓库 Maven提供的默认远程仓库
		- 私服 架设在局域网内的仓库服务，代理广域网上的远程仓库
		- 其他公共库


##生命周期和插件
Maven的生命周期对所有项目的构建过程进行抽象和统一，包括项目的清理、初始化、编译、测试、打包、集成测试、验证、部署、站点生成等构建步骤。
生命周期抽象了构建的各个步骤，定义了他们的次序，但没有提供具体实现，具体实现由插件来完成。每个构建步骤都可以绑定一个或多个插件行为。Maven为大多数构建步骤编写并绑定了默认插件。  
###三种生命周期
Maven包含三种生命周期，分别为clean、default、site。每个生命周期包含一些阶段，这些阶段是有顺序的，并且后面的阶段依赖于前面的阶段。 如当调用clean的时候，会顺序执行pre-clean、clean
- clean 用来清理项目
 	- pre-clean 执行一些清理前需要完成的工作
 	- clean 清理上一次构建生成的文件
 	- post-clean 执行一些清理后需要完成的工作
- default 用来构建项目
	- validate
	- initialize
	- generate-sources
	- process-sources 处理项目主资源文件。主要是将src/main/resources目录的内容处理后输出到classpath目录
	- generate-resources
	- process-resources
	- compile 编译项目源码。主要是编译src/main/java目录下的文件输出到classpath目录
	- process-classes
	- generate-test-sources
	- process-test-sources
	- generate-test-resources
	- process-test-resources 
	- test-compile 编译项目测试代码。主要是编译src/test/java目录下的文件输出到classpath目录
	- process-test-classes
	- test 使用单元测试框架运行测试代码
	- prepare-package
	- package
	- pre-integration-test
	- integration-test
	- post-integration-test
	- verify
	- install 将包安装到Maven本地仓库
	- deploy 将包复制到远程仓库
- site 建立和发布项目站点
	- pre-site
	- site 生成项目站点文档
	- post-site
	- site-deploy 将生成的站点发布到服务器上
###插件
Maven的核心仅定义了抽象的生命周期，具体的任务都是交由插件来完成的，插件都是以独立的构件形式存在。

##聚合与继承
###聚合
聚合应用在多模块的项目中，使用一条命令构建多个项目模块，而不需要分别构建多个模块  
新建一个空项目，设置pom.xml的GAV等必要属性，packaging元素值必须为*pom*。使用modules元素引用多个模块  
`...`  
`<packaging>pom</packaging>`  
`<modules>`  
　　`<module>{module-artifactId1}</module>`  
　　`<module>{module-artifactId2}</module>`  
　　　　`...`  
`</molules>`  
`...`

###继承
在面向对象的思想中，会把共性内容抽象独立出来。Maven也沿用了这一思想，叫做继承  
新建一个空项目，作为抽象的顶层，供其他模块继承，设置pom.xml的GAV等必要属性，packaging元素值必须为*pom*。将共性依赖等配置在该文件中。子模块通过parent元素指定父模块的GAV及其相对的pom文件路径  
`<parent>`  
	　　`<groupId></groupId>`  
    　　`<artifactId></artifactId>`  
    　　`<version></version>`  
    　　`<relativePath>../pom.xml</relativePath> <!-- 父模块pom文件相对路径，默认../pom.xml-->`  
`</parent>`

子模块可以继承属性：
- groupId 项目组Id
- version 项目版本
- description 项目描述
- organization 项目的组织信息
- inceptionYear 项目创始年份
- url 项目URL
- developers 项目开发者信息
- contributors 项目贡献者信息
- distributionManagement 提供了统一管理依赖版本号的方式。一般在父项目的pom中定义依赖，子项目只需要引用依赖的GA属性而不用写版本号。此外，distributionManagement只是生命依赖，并不会引入依赖，所以需要子项目显示引入依赖
- issueManagement 缺陷跟踪系统信息
- ciManagement 持续集成系统信息
- scm 版本控制系统信息
- mailingLists 项目邮件列表信息
- properties 自定义的Maven属性
- dependencies 依赖配置
- dependencyManagement 依赖管理配置
- repositories 仓库配置
- build 源码目录配置、输出目录配置、插件配置、插件管理配置等
- reporting 项目报告输出目录配置、报告插件配置等



##命令
- mvn clean 清理，会把target文件夹中的class文件等删除
- mvn compile 将代码编译到target文件中
- mvn test 运行测试
- mvn package 打包
- mvn install 将打好的包放在本地仓库中，供其他项目使用
- mvn deploy 发布到私有服务器上
- mvn archetype:generate 创建文件目录（骨架）

##FAQ
- 执行maven test，控制台log中文乱码  
在pom.xml中添加：  
`<properties>`  
　　`<argLine>-Dfile.encoding=UTF-8</argLine>`  
`</properties>`

##附录
- GAV查询网址：  
	- [http://mvnrepository.com/](http://mvnrepository.com/ "http://mvnrepository.com/")  
	- [http://www.sonatype.org/](http://www.sonatype.org/ "http://www.sonatype.org/")  