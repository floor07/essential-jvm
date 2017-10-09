*  jps（JVM Process Status）虚拟机进程状态工具

jps命令格式：

jps \[options\] \[hostid\]

| option | 作用 |
| :--- | :--- |
| -q | 只输出LVMID，省略主类的名称。 |
| -m | 输出虚拟机启动时传递给主类main（）函数的参数 |
| -l | 输出主类全名，如果jar执行，输出jar的路径 |
| -v | 虚拟机启动时的JVM参数 |

*  jstat 虚拟机统计信息监控工具

jstat命令格式为：

jstat \[ option vimd \[interval\[s\|ms\]\] \[count\] \]

常用 jstat -gc vimd \[interval\[s\|ms\]\] \[count\] \]

  


| 显示列名 | 具体描述 |
| :--- | :--- |
| S0C | 年轻代中第一个survivor（幸存区）的容量 \(字节\) |
| S1C | 年轻代中第二个survivor（幸存区）的容量 \(字节\) |
| S0U | 年轻代中第一个survivor（幸存区）目前已使用空间 \(字节\) |
| S1U | 年轻代中第二个survivor（幸存区）目前已使用空间 \(字节\) |
| EC | 年轻代中Eden的容量 \(字节\) |
| EU | 年轻代中Eden目前已使用空间 \(字节\) |
| OC | Old代的容量 \(字节\) |
| OU | Old代目前已使用空间 \(字节\) |
| PC | Perm\(持久代\)的容量 \(字节\) |
| PU | Perm\(持久代\)目前已使用空间 \(字节\) |
| YGC | 从应用程序启动到采样时年轻代中gc次数 |
| YGCT | 从应用程序启动到采样时年轻代中gc所用时间\(s\) |
| FGC | 从应用程序启动到采样时old代\(全gc\)gc次数 |
| FGCT | 从应用程序启动到采样时old代\(全gc\)gc所用时间\(s\) |
| GCT | 从应用程序启动到采样时gc用的总时间\(s\) |

  


*  jinfo :Java配置信息工具

*  jmap 内存镜像工具

命令格式：

jmap \[option\] vmid

| 选项 | 作用 |
| :--- | :--- |
| -dump | 生成Java堆转储对象快照。格式为：-dump:\[live, \]format=b,file=&lt;filename&gt;,其中live子参数表示是否只dump出存活的对象 |
| -finalizerinfo | 显示在F-Queue中等待Finalizer线程执行finalize方法的对象。只在Linux/Solaris平台下有效 |
| -heap | 显示Java堆详细信息，如使用哪种回收器，参数配置分代状况等。只在Linux/Solaris平台下有效。 |
| -histo | 显示堆中对象统计信息，包括：类，实例，合计容量 |
| -permstat | 以Classloader为统计口径，显示永久代内存状态。只在Linux/Solaris平台下有效。 |
| -F | 当dump没有反应，可以强制生成快照。只在Linux/Solaris平台下有效。 |

  


*  jhat 虚拟机转储快照分析工具（非常简陋，一般不使用）

*  jstack 堆栈跟踪工具

显示当前时刻线程快照。

命令： jstack \[option\] vmid

| 选项 | 作用 |
| :--- | :--- |
| -F | 不响应时，可以强制生成快照 |
| -l | 显示关于锁的附加信息 |
| -m | 如果调用本地方法，显示C/C++的堆栈 |

*  hsdis JIT生成反汇编



