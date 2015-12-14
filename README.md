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
　　　　`<optional></optional> <!--标记依赖是否可选-->`  
　　　　`<exclusions> <!--排除传递性依赖-->`  
　　　　　　`<exclusion>`  
　　　　　　`...`  
　　　　　　`</exclusion>`  
　　　　`</exclusions>`  
　　`</dependency>`  
`</dependencies>` 

###传递性依赖
假设A依赖于B，B依赖于C，则A对于C是传递性依赖。我们称A对于B是第一直接依赖，B对于C是第二直接依赖  
|第一直接依赖/第二直接依赖|  compile | test | provided | runtime |
|---------------------|----------|------|----------|---------|
|       compile       |  compile |   -  |     -    | runtime |
|      	  test        |   test   |   -  |     -    |   test  |
|       provided      | provided |   -  | provided | provided|
|        runtime      |  runtime |   -  |     -    | runtime |


##命令
- mvn clean 清理，会把target文件夹中的class文件等删除
- mvn compile 将代码编译到target文件中
- mvn test 运行测试
- mvn package 打包
- mvn install 将打好的包放在本地仓库中，供其他项目使用
- mvn deploy 发布到私有服务器上
- mvn archetype:generate 创建文件目录（骨架）

4 依赖
4.1 设置依赖
	`<dependencies>`  
	`	<dependency>`  
	`		<groupId></groupId>`  
	`		<artifactId></artifactId>`  
	`		<version></version>`  
	`		<scope></scope> <!--默认compile-->`  
	`	</dependency>`  
	`</dependencies>`  
4.2 依赖具有传递性
A-->C B-->A ==> B-->C
scope:
test 测试范围有效 编译和打包时，不会使用这个依赖
compile（默认） 编译打包有效
provided 编译测试有效
runtime 运行时依赖，编译时不依赖
4.3 依赖传递性冲突：
case 1 
a --> b1.0
c --> b2.0
d --> a, c 按先后顺序依赖， 即依赖b1.0

case 2
a --> b1.0
c --> b2.0
d --> a, c
f --> d, c 如果路径长短不一致，按最短路径，即依赖b2.0

如果希望精确的控制依赖包，可以使用依赖的排除功能
<dependency>
		<groupId></groupId>
		<artifactId></artifactId>
		<version></version>
		<exclusions>
			<exclusion>{GAV}</exclusion>
		</exclusions>
</dependency>

4.4 聚合依赖，继承（POM project object module）
新建一个项目只保留pom.xml文件，通过
<packaging>pom</packaging>
<modules>
	<module></module>
</modules>
来引用模块, 然后统一打包部署


5 仓库
创建私有服务器

6 生命周期和插件


##附录
- GAV查询网址：  
	- [http://mvnrepository.com/](http://mvnrepository.com/ "http://mvnrepository.com/")  
	- [http://www.sonatype.org/](http://www.sonatype.org/ "http://www.sonatype.org/")  