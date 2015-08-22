# StartFromZero-Maven
Maven 多模块项目管理
1 安装maven（maven实战）
安装maven 并配置环境变量
配置本地仓库路径，修改conf/setting.xml文件的localRepository节点（全局的）
或者再本地仓库目录中新建setting.xml文件，localRepository节点设置为当前路径
以上配置可通过eclipse可视化界面操作

2 节点介绍：
<groupId></groupId> 项目名称
<artifactId></artifactId> 模块名称 通常项目名称-模块名称
<version></version> 版本号
GAV: 以上三个值确定一个依赖
GAV查询网址：
http://mvnrepository.com/
http://www.sonatype.org/

3 命令：
mvn clean 清理，会把target文件夹中的class文件等删除
mvn compile 将代码编译到target文件中
mvn test 运行测试
mvn package 打包
mvn install 将打好的包放在本地仓库中，供其他项目使用
mvn deploy 发布到私有服务器上
mvn archetype:generate 创建文件目录（骨架）

4 依赖
4.1 设置依赖
<dependencies>
	<dependency>
		<groupId></groupId>
		<artifactId></artifactId>
		<version></version>
		<scope></scope> <!--默认compile，编译范围依赖-->
	</dependency>
</dependencies>
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
