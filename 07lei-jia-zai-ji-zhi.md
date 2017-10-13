

**JVM中的类的唯一性：**

由类加载器+和这个类本身确定。

**双亲委派模型**

![](/assets/shuangqiweipai.png)

&lt;!--  
 /\* Font Definitions \*/  
 @font-face  
	{font-family:"Cambria Math";  
	panose-1:2 4 5 3 5 4 6 3 2 4;  
	mso-font-charset:1;  
	mso-generic-font-family:roman;  
	mso-font-format:other;  
	mso-font-pitch:variable;  
	mso-font-signature:0 0 0 0 0 0;}  
@font-face  
	{font-family:微软雅黑;  
	panose-1:2 11 5 3 2 2 4 2 2 4;  
	mso-font-charset:134;  
	mso-generic-font-family:swiss;  
	mso-font-pitch:variable;  
	mso-font-signature:-2147483001 684670034 22 0 262175 0;}  
@font-face  
	{font-family:"\@微软雅黑";  
	panose-1:2 11 5 3 2 2 4 2 2 4;  
	mso-font-charset:134;  
	mso-generic-font-family:swiss;  
	mso-font-pitch:variable;  
	mso-font-signature:-2147483001 684670034 22 0 262175 0;}  
 /\* Style Definitions \*/  
 p.MsoNormal, li.MsoNormal, div.MsoNormal  
	{mso-style-unhide:no;  
	mso-style-qformat:yes;  
	mso-style-parent:"";  
	margin:0cm;  
	margin-bottom:.0001pt;  
	mso-pagination:none;  
	font-size:10.5pt;  
	mso-bidi-font-size:11.0pt;  
	font-family:"微软雅黑","sans-serif";  
	mso-bidi-font-family:"Times New Roman";}  
.MsoChpDefault  
	{mso-style-type:export-only;  
	mso-default-props:yes;  
	mso-ascii-font-family:微软雅黑;  
	mso-fareast-font-family:微软雅黑;  
	mso-hansi-font-family:微软雅黑;  
	mso-font-kerning:0pt;}  
 /\* Page Definitions \*/  
 @page  
	{mso-page-border-surround-header:no;  
	mso-page-border-surround-footer:no;}  
@page Section1  
	{size:612.0pt 792.0pt;  
	margin:72.0pt 90.0pt 72.0pt 90.0pt;  
	mso-header-margin:36.0pt;  
	mso-footer-margin:36.0pt;  
	mso-paper-source:0;}  
div.Section1  
	{page:Section1;}  
--&gt;  


类加载请求时，先委托给父类进行加载，父加载器也是如此。当父类加载器不能加载事，当前类才进行加载。

**tomcat正统加载模型**

tomcat的类加载器介绍

| 类加载器名称 | 加载的目录 | 备注 |
| :--- | :--- | :--- |
| BootStrap ClassLoader | ${java\_home}/lib | JDK默认 |
| Extension ClassLoader | ${java\_home}/lib/ext | JDK默认 |
| Application ClassLoader | classpath下jar，或class | JDK默认 |
| Common ClassLoader | 可以设置在catalina.properties中的common.loader进行设置 | tomcat默认是${catalina.base}/lib/\*${catalina.home}/lib/\* |
| Catalina ClassLoader | 可以设置在catalina.properties中server.loader= | tomcat默认未设置 |
| Shared ClassLoader | shared.loader= | tomcat默认未设置 |
| webapp ClassLoader | webapp/WEB-INF/\*下的java文件 |  |
| Jasper（Jsp） Classloader | webapp/WEB-INF/\*下的jsp文件 | JSP文件被修改后，会替换当前的Jasper ClassLoader,用新Jasper ClassLoader加载修改后的jsp |

对应的结构图如下：

![](/assets/tomcatclassLoader.png)

破坏双亲委派模型

1.java1.2之前的程序做兼容 向前兼容

2.JNDI等，（允许父类加载器委托子类加载器加载，打通了双亲委派模型的逆向）。

 （使用，线程上下文加载器 Thread Context ClassLoader，这个由java.lang.Thread中的setContextClassLoader\(\)方法进行设置）

3.OSGi（热部署可以平级查找）：

举例说明：

Bundle A：声明发布了packageA，依赖了java\_\*的包

Bundle B：声明依赖于packageA和packageC，同时也依赖java.\*包

Bundle C：声明发布了packageC，依赖了packageA

其对应的结构图如下：

![](/assets/bundle.png)

OSGi加载规则如下：

将ava.\*开头的委派给父类加载器。

否则委派列表名单内的类，委派给父类加载器。

否则Import列表中的类委托给Export这个类的Bundle加载器

否则Bundle的classPath，自己的加载器。

否则，查找类是否在自己的Fragment Bundle中，如果在则,委托给Fragment Bundle的类加载器

否则，查找Dynamic Import列表的Bundle，委托给对应的Bundle加载

否则，查找失败。

