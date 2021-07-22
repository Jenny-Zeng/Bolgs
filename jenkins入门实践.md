# Jenkins入门实战
今天，要和大家分享Jenkins的基本使用。本文整理了一些在日常工作中经常用到的功能和插件，在此分享给大家。
### 概念
Jenkins，最早被称为Hudson，是一个java语言编写的开源的、提供友好操作界面的持续集成（CI，Continuous integration）工具，何为持续集成？就是它可以监控版本控制系统的变化。举一个简单的场景，无论任何时候，只要检测到有变化，那么Jenkins就会自动编译和测试我们的应用程序。如果出现了任何问题，它会通知开发人员，以便他们感知并解决这个问题。
### 特征
- 开源的Java语言持续集成工具，支持持续集成，持续部署
- 易于安装部署配置，可通过yum或者war包以及用过docker容器等快速实现安装部署，可方便web界面配置管理。
- 消息通知及测试报告，集成RSS/E-mail通过RSS发布构建结果或构建完成时通过e-mail通知，生成IUnit/TestNG测试报告
- 分布式构建：支持Jenkins能够让多台计算机一起构建/测试
- 丰富的插件安装：支持扩展插件，

### 准备工作

在开启实践之前，我们需要做一些准备工作

- 安装JDK
- 安装Git
- 创建Github
- 配置SSH密钥，建立计算机和GitHub服务器之间的连接
- 安装Jenkins
### 入门实践 

Jenkins提供了多种项目类型，本文以freestyle类型为例。在新建一个项目之后，我们会看到下面的页面。

![jenkins-1](https://github.com/Jenny-Zeng/Bolgs/blob/main/pics/jenkins-1.png)

主要包含四大功能模块，下面一一展开描述：
- General:设置全局性的选项，比如项目名、描述信息等，在此列举两个经常使用的功能。

  - Discard old builds：丢弃旧的构建，构建job会消耗大量的磁盘空间，尤其是存储的构建产物，所以该项允许我们限制在构建历史记录的作业数。可以告诉Jenkins只保留最近的构建或者保留不超过指定数量大构建，如果某个构建有特殊价值，则在**构建细节页面**右上角点击 Keep this build forever，意思是告诉Jenkins永远保留这个构建。

  - This project is parameterized：设置一些参数（包括String、boolean、Choice、Git参数等）

- Source Code Managemant，配置源码管理，持续集成服务器最基本的作用就是监控版本控制系统，定期检出并构建源码的最新版本，输入想要构建的Git 仓库和Credentials，在Branches specifier中，默认值为*/master，如果为空，则所有的分支都会被构建，改语法支持通配符或者特定分支书写，下面列举两个例子：

  - refs/heads/BranchName：根据分支进行构建
  - \**/tags/**：通过GitHub中的Tag进行触发Jenkins构建，

  ![jenkins-source](https://github.com/Jenny-Zeng/Bolgs/blob/main/pics/jenkins-source.png)

- Build Triggers，构建触发器，Jenkins提供了时间轮寻方式，有三种基本方式可以触发构建

  - Build after other projects are built：另一个构建job完成之后，触发本次构建job
  - Build periodically，不管版本库代码是否发生变化都周期性执行构建。
  - Poll SCM：仅当SCM收到变化时，才会触发Jenkins构建。

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

- Build：构建步骤，告诉Jenkins想要如何构建项目，Jenkins提供了多种方式
  - Execute shell：执行特定的shell脚本，注意：只需要填写相对于工作目录的脚本名称，不过在构建步骤中尽量避免使用系统级别的脚本，这样可以减少对机器配置的依赖，一个方便的替代方式是使用Groovy或者Gant执行系统脚本。
  - Copy artifacts from another project
  - Trigger/call builds on other projects

- Post-build Actions,构建后操作，在构建完成之后，可能需要归档某些生成的构建产物，报告测试结果，并把相关结果通知给相关人员。
  - Archive the artifacts
    可以使用通配符结构归档构建产物，例如 \**/*.zip，如果有多个文件进行归档，可以使用逗号进行分隔。
   
  - Editable Email Notification
    邮件通知，在Project Recipient List中输入相关人员的邮箱，发送的内容可在“Default Content”中以HTML的语法结构输入。
 



## jenkins中参数的使用

在工具栏有一个 This project is parameterized,其中提供了很多的参数

![parameter](https://github.com/Jenny-Zeng/Bolgs/blob/main/pics/parameter.png)

1. String Parameter

   字符串类型，可以作为环境变量使用

2. Boolean Parameter

   ![image-20210722113509708](https://github.com/Jenny-Zeng/Bolgs/blob/main/pics/image-20210722113509708.png)

   Boolean类型可以用于构建时的判断，当勾选Default Value时，默认值为ture。

   在构建的脚本中我们可以书写：

   ```
   if [ $isTure = true ]
   then
   	echo "上传到fir"
    fir publish XXXX
   else
   	echo "暂不上传"
   fi
   ```

3. Extended Choice Parameter

   Jenkins中提供了单选和多选项，在设置的过程中，我们需要在Choose Source for Value中自定义选项。

   ![image-20210722114240169](https://github.com/Jenny-Zeng/Bolgs/blob/main/pics/image-20210722114240169.png)

   其中，提供了很多的类型，其中有单选、多选、复选框，展示如下，设置不同的参数类型就会有不同的展示效果。

   ![image-20210722135124265](https://github.com/Jenny-Zeng/Bolgs/blob/main/pics/image-20210722135124265.png)

   ![image-20210722115158461](https://github.com/Jenny-Zeng/Bolgs/blob/main/pics/image-20210722115158461.png)

4. ![image-20210722120516422](https://github.com/Jenny-Zeng/Bolgs/blob/main/pics/image-20210722120516422.png)


### 系统环境变量
- WORKSPACE
    工作空间的绝对路径
- BUILD_NUMBER
  当前的构建号，比如 “8”。
- JOB_NAME
  当前job的名字，比如Test-demo。
- BUILD_TAG
  为了便于识别当前构建的job，比如jenkins-Test-demo-8。
- NODE_NAME
  如果构建运行在slave上，则输出该节点的名字，如果构建运行在master上，则输出master。
- NODE_LABELS
  运行该构建节点标签列表
- JENKINS_HOME
  主节点上为Jenkins存储数据分配目录的绝对路径，比如 /Users/xxxx/.jenkins
- JENKINS_URL
  jenkins服务器完整的URL，比如 http://79d3725b30cf.ngrok.io
- JOB_URL
  当前构建job完整的 URLhttp://79d3725b30cf.ngrok.io/job/Test-demo/
- BUILD_URL
  当前构建完整的URL，比如 http://79d3725b30cf.ngrok.io/job/Test-demo/8/

