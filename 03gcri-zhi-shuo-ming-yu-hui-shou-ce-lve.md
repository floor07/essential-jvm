- GC日志说明

\`\`\`

\[GC

 \[DefNew: 6906K-

&gt;

6906K\(9216K\), 0.0000140 secs\]

 \[Tenured: 6144K-

&gt;

8192K\(10240K\), 0.0113843 secs\] 13050K-

&gt;

10662K\(19456K\),

 \[Perm : 224K-

&gt;

224K\(12288K\)\], 0.0114603 secs\]

\[Times: user=0.00 sys=0.00, real=0.00 secs\]

\`\`\`

1. \[GC 区分GC的类型，\[GC未发送Stop-the-World \[Full GC发送了Stop-the-World

2. \[DefNew 新生代 \[Tenured 老年代 \[Perm 永久代 表示GC发送的区域，名称由回收器决定。

 \[DefNew表示Serial回收器，\[ParNew表示ParNew回收器 \[PSYoungGen为Parallel Scavenge回收器

3. 6906K-

&gt;

6906K\(9216K\) 回收前大小-

&gt;

回收后大小（该区域总大小）

4. \[\]外的13050K-

&gt;

10662K\(19456K\) GC前堆使用内存-

&gt;

GC后堆使用内存（JVM堆内存总大小）

5. 0.0113843 secs 该区域垃圾回收所用时间

6. Times: user=0.00 sys=0.00, real=0.00 secs

 用户态消耗的cpu时间，系统态消耗的cpu时间，从开始到结束经过的时间

 多线程操作时间会叠加，user，sys，超过real很正常。

  


- 回收策略说明

- - 对象优先分配到eden区域

- - 大对象直接进入老年代，

 -XX:PretenureSizeThreshold参数，单位byte，当分配的对象大于设置值时，进入老年代

- - 长期存活对象进入老年代

 -XX:MaxTenuringThreshold=15 进行设置，每熬过一次Minor GC年龄+1.

- - 动态年龄判断

 Survivor空间中相同年龄所有对象大小的总和大于Survivor空间的一般，

 年龄大于等于该年龄的对象之间进入老年代。

- - 空间分票担保

 老年代的连续空间大于新生代对象总大小

 或者历次进入老年代的对象的平均大小，就会进行Minor GC

 否则进行Full GC

  


  


