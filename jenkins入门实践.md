# Jenkins入门实战

今天，要和大家分享Jenkins的基本使用，首先我们需要了解什么是Jenkins，以及它可以为我们做些什么？

Jenkin是一个开源的、提供友好操作界面的持续集成（CI）工具，

Jenkins，最早被称为Hudson是一个java语言编写的开源的、提供友好操作界面的持续集成（CI，Continuous integration）工具，何为持续集成？就是它可以监控版本控制系统的变化。举一个简单的场景，无论任何时候，只要检测到有变化，那么Jenkins就会自动编译和测试我们的应用程序。如果出现了任何问题，它会通知开发人员，以便他们感知并解决这个问题。

在开启实战之旅之前我们应该准备必要的环境，比如安装java、安装Git、创建GitHub、配置SSH密钥等。

### 准备工作

在开启实践之前，我们需要做一些准备工作

- 安装JDK
- 安装Git
- 创建Github
- 配置SSH密钥，建立计算机和GitHub服务器之间的连接
- 安装Jenkins

在Jenkins官网安装合适的版本，接下来我们开始迈入Jenkins大门。

构建第一个Jenkins job，首先Jenkins提供了很多类型的项目，我们首选freestyle类型，

![jenkins-1](file:///Users/zengzhaoxia/Library/Application Support/typora-user-images/image-20210702115247544.png?lastModify=1626934157)

- General:可以设置一些全局性的选项，在此列举两个经常使用的功能。

  - Discard old builds：丢弃旧的构建，意思是，对于一个项目的每次构建，都会保留每次的构建信息，当我们只想要最近的三次构建信息，只需要把把保留最大构建数据设置为3即可。

  - This project is parameterized：设置一些参数（包括String、boolean、Choice、Git参数等）

- Source Code Managemant，输入想要构建的repo 和Credentials，在Branches specifier中，默认值为*/master，不过在实际工作中，我们可能通过分支或者tag进行构建，分别有如下写法：

  - refs/heads/BranchName：根据分支进行构建
  - \**/tags/**：通过GitHub中的Tag进行触发Jenkins构建，

  ![jenkins-source](/Users/zengzhaoxia/Library/Application Support/typora-user-images/image-20210722143609094.png)

- Build Triggers，在这个模块中可以设置定时构建，就是在什么条件下会触发构建，其中这几个功能

  - Build periodically，不管版本库代码是否发生变化都周期性执行构建。
  - Poll SCM：根据设定的时间比较源码，如果发生变更，则进行构建。

  注意：Build periodically和Poll SCM的语法是相同的，

  ```
  结构：* * * * *
  第一颗：分钟（0～59）
  第二颗：小时（0～23）
  第三颗：一个月的第几天（1～31）
  第四颗：第几月（1～12）
  第五颗：一周的第几天（0～7），其中0和7代表的都是周日
  例如：
  半小时查询一次：H/30 * * * *
  每分钟查询一次：*/1 * * * *
  ```

  - Github hook trigger for GITScm polling

    通过webhook进行触发，该操作需要在GitHub中设置webhook，以及勾选触发条件。

- Build Environment，设置构建环境，在此列举两个常见的小功能

  - Delete workspace before build starts,在开启本次构建之前，先删除现有的工作空间。这种情况要是视情况而定。如果项目改动比较大的时候，需要进行删除操作。
  - Add timestamps to the Console Output：在控制台最左侧一列添加输出日志的时间。

- Build

### Jenkins的特性

- 开源的Java语言持续集成工具，支持持续集成，持续部署
- 易于安装部署配置，可通过yum或者war包以及用过docker容器等快速实现安装部署，可方便web界面配置管理。
- 消息通知及测试报告，集成RSS/E-mail通过RSS发布构建结果或构建完成时通过e-mail通知，生成IUnit/TestNG测试报告
- 分布式构建：支持Jenkins能够让多台计算机一起构建/测试
- 文件识别：Jenkins能够跟踪哪次构建生成哪些jar，哪次构建使用哪个版本的jar等。
- 丰富的插件安装：支持扩展插件，





## jenkins中参数的使用

在工具栏有一个 This project is parameterized,其中提供了很多的参数

![parameter](/Users/zengzhaoxia/Bolgs/pics/parameter.png)

1. String Parameter

   字符串类型，可以作为环境变量使用

2. Boolean Parameter

   ![image-20210722113509708](/Users/zengzhaoxia/Library/Application Support/typora-user-images/image-20210722113509708.png)

   Boolean类型可以用于构建时的判断，当勾选Default Value时，默认值为ture。

   在构建的脚本中我们可以书写：

   ```
   if [ $isTure == true ]
   then
   	echo "上传到fir"
       fir publish XXXX
   else
   	echo "暂不上传"
   fi
   ```

3. Extended Choice Parameter

   Jenkins中提供了单选和多选项，在设置的过程中，我们需要在Choose Source for Value中自定义选项。

   ![image-20210722114240169](/Users/zengzhaoxia/Library/Application Support/typora-user-images/image-20210722114240169.png)

   其中，提供了很多的类型，其中有单选、多选、复选框，展示如下，设置不同的参数类型就会有不同的展示效果。

   ![image-20210722135124265](/Users/zengzhaoxia/Library/Application Support/typora-user-images/image-20210722135124265.png)

   ![image-20210722115158461](/Users/zengzhaoxia/Library/Application Support/typora-user-images/image-20210722115158461.png)

4. ![image-20210722120516422](/Users/zengzhaoxia/Library/Application Support/typora-user-images/image-20210722120516422.png)





### Jenkins中插件的使用

1. 

