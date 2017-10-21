---

layout: post # 使用的布局（不需要改）

title: VisualVM使用中可能遇到的ERROR 62 # 标题 

subtitle: VisualVM之error 62 #副标题

date: 2017-10-21 # 时间

author: holynova-SD # 作者

header-img: img/post-bg-2015.jpg #这篇文章标题背景图片

catalog: true # 是否归档

tags: #标签

 - VisualVM

 - Java

 - Software Engineering

---

# Redefinition failed with error 62

>本文写成时，使用的Java版本为1.8.0_121，VisualVM版本为1.3.9.

在Java1.8版本下，使用eclipse编写Java工程并运行，并以1.3.9版本VisualVM进行监控，有时候VisualVM的profiler在检测CPU时会报以下错误：

```
Redefinition failed with error 62
Check JVMTI Documentation for this error code
```
这时候一种可行的解决办法如下：

## 命令行

如果是在命令行下运行jar包，则可在后面添加如下参数（假设运行的jar包名为a.jar）：
```
java -jar a.jar -Xverify:none
```

## Eclipse

如果是在Eclipse中，则可以把刚才的参数添加到运行设置中。步骤如下：

①打开Eclipse，右击项目名称，选择Run as -> Run Configurations...，选择。
<img src="https://github.com/holynova-SD/Blog-Pictures/blob/master/2017-10-21/pic1.png?raw=true" alt="Loading..."/>

②确定左侧是要更改的项目，选择(x) = Arguments选项框，在下面的VM arguments中添加:
```
-Xverify:none
```
<img src="https://github.com/holynova-SD/Blog-Pictures/blob/master/2017-10-21/pic2.png?raw=true" alt="Loading..."/>
如图所示，添加好后Apply即可。