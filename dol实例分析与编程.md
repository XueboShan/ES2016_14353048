#实验二
***
###一、实验内容
1. 修改example2，让3个square模块变成2个, tips:修改xml的iterator
2. 修改example1，使其输出3次方数，tips:修改square.c

##example1

**1. examp1的整个过程为：**

*  首先通过generator生产出从1~19的一串序列
* 每生产出一个数，就通过p传到squre类中
* 在squre类中对每个数做一个平方运算
* 然后，squre类再通过p进程将平方后的数传到consumer类中
* 最后consumer类将这些字符串输出到控制台上。


**2. 现在从生产者开始分析代码：**
```cpp
void generator_init(DOLProcess *p) {
    p->local->index = 0;
    p->local->len = LENGTH;
}

int generator_fire(DOLProcess *p) {

    if (p->local->index < p->local->len) {
        float x = (float)p->local->index;
        DOL_write((void*)PORT_OUT, &(x), sizeof(float), p);
        p->local->index++;
    }

    if (p->local->index >= p->local->len) {
        DOL_detach(p);
        return -1;
    }

    return 0;
}
```
 * 首先是初始化函数，意在为进程p设置执行次数。
 这个函数只会执行一次，所以p->local->index的值首先被设为0，然后设置总长度为20，作为程序执行次数。
 然后是信号发生函数，生产者通过这个函数发送信号给square函数。
 首先判断是否已经达到执行上限次数，如果没有，那么就进行以下步骤：
	 1. 获取当前进程的位置
	 2. 然后第一个作为输出信号利用DOL_write从PORT_OUT端口将信号通过第一个switch_channel发送给square
	 3. 做完这些操作之后，将位置向后移动一位。	
     4. 如果已经达到设置的执行次数，那么就停止执行生产者线程。      
 
**3.  现在从squre线程开始分析代码：**

* 经过生产者之后，square线程接收到信号，并对信号进行操作
```cpp
int square_fire(DOLProcess *p) {
    float i;

    if (p->local->index < p->local->len) {
        DOL_read((void*)PORT_IN, &i, sizeof(float), p);
        i = i*i*i;
        DOL_write((void*)PORT_OUT, &i, sizeof(float), p);
        p->local->index++;
    }

    if (p->local->index >= p->local->len) {
        DOL_detach(p);
        return -1;
    }

    return 0;
}
```
* 和生产者一样，首先判断进程位置是否超过长度，如果没有，那么就执行以下操作：
1. 通过函数DOL_read 从PORT_IN端口读取数据，赋值给i
2. 在函数中对i进行立方操作
3. 然后将改写的数据利用DOL_write函数从输出端口PORT_OUT经过第二个switch_channel发送到消费者
4. 做完这些操作之后，将位置向后移动一位（注意，三个线程的index值不会相互影响）。
5. 如果已经达到长度，那么就停止执行square线程。

**4. 下面分析comsumer线程的代码：**
```cpp
int consumer_fire(DOLProcess *p) {
    float c;
    if (p->local->index < p->local->len) {
        DOL_read((void*)PORT_IN, &c, sizeof(float), p);
        printf("%s: %f\n", p->local->name, c);
        p->local->index++;
    }

    if (p->local->index >= p->local->len) {
        DOL_detach(p);
        return -1;
    }

    return 0;
}
```
* 首先是判断位置是否小于长度，如果小于，就执行以下步骤：
    1. 通过函数DOL_read 从PORT_IN端口读取数据，赋值给c
    2. 将读取到的数据输出
    3. 将位置后移一位
* 如果已经达到长度，那么就停止执行消费者线程。

**5. example1实验结果：**

* 由上述分析我们可以看的出来，最终处理过的数据是在消费者中输出的，处理数据的过程是在square中，所以实验要求让我们将平方变成三次方，我们只需要在处理数据的时候将i=i*i改为i=i*i*i，就能达到输出3次方数的目的。
* dot图流程不变，如下图所示：
![final1](https://cloud.githubusercontent.com/assets/22719868/19885413/6274ae5c-a059-11e6-94ac-7a185cbd8681.png)
* 实验结果如下图所示：
![example1](https://cloud.githubusercontent.com/assets/22719868/19885403/5aa48d5a-a059-11e6-9927-87d2de857670.png)

* 可以看到，由于传递的是位置（0-19），所以每一个输出对应的都是0-19中一个数字的立方数。

***
##example2：

 **1. 由于exmaple2的生产者、消费者、平方的代码和exmple1基本一样，所以这里只分析xml文件：**

* 这个xml文件相比example1 的不同之处就在于这里用到了循环，
所以重点在于理解循环，代码的主要过程如下：
```xml
<variable value="2" name="N"/>

  <!-- instantiate resources -->
  <process name="generator">
    <port type="output" name="10"/>
    <source type="c" location="generator.c"/>
  </process>

  <iterator variable="i" range="N">
    <process name="square">
      <append function="i"/>
      <port type="input" name="0"/>
      <port type="output" name="1"/>
      <source type="c" location="square.c"/>
    </process>
  </iterator>

  <process name="consumer">
    <port type="input" name="100"/>
    <source type="c" location="consumer.c"/>
  </process>

  <iterator variable="i" range="N + 1">
    <sw_channel type="fifo" size="10" name="C2">
      <append function="i"/>
      <port type="input" name="0"/>
      <port type="output" name="1"/>
    </sw_channel>
  </iterator>
```

* 前面代码是对生产者、消费者、square的定义。
* 接下来是对switch_channel的命名:

&emsp;&emsp;首先第一句的意思应该是说，i在0<= i < N+1的范围中进行迭代。在循环中，函数首先设置通道的传输模式，然后设置它的编号，名字统一是C2，但是在尾部添加了数据i，代表第i条通道，这样一来，就完成了对通道的命名(C2_0, C2_1)。

* 接下来就是将自定义数目的square模块连接起来，这里也用到了一个循环。
循环分为两大步：
```xml
<!-- instantiate connection -->
  <iterator variable="i" range="N">
    <connection name="to_square">
      <append function="i"/>
      <origin name="C2">
        <append function="i"/>
        <port name="1"/>
      </origin>
      <target name="square">
        <append function="i"/>
        <port name="0"/>
      </target>
    </connection>

    <connection name="from_square">
        <append function="i"/>
        <origin name="square">
          <append function="i"/>
          <port name="1"/>
        </origin>
        <target name="C2">
          <append function="i + 1"/>
          <port name="0"/>
        </target>
    </connection>
  </iterator>
```
* 第一步是将C2_i通道和square模块相连接。
* 第二步是将square模块和C2_i+1通道相连接，用图像来解释的话，这一步所描述的大概是这样的意思。
所以将一个个这样的模块连接起来之后中间的square模块就相互连接起来了。
* 连接完中间之后，再将两端的switch_channel连接上头尾就完成了对dot图的构建。

**2. 而本次实验需要让我们将三个模块变成两个模块，所以根据上述原理，我们需要改动的是变量N，N的赋值在文件最开始的部分，即下述语句：** <br></br>

* 重新编译运行之后，在/home/wangkai/dol/build/bin/main/example2 目录可以看到.dot
文件，下载xdot之后，即可看到下面的流程图。<br></br>
<br></br>![final2](https://cloud.githubusercontent.com/assets/22719868/19885417/65b48d58-a059-11e6-950c-a4dd0fd08d45.png)<br></br>

* 而输出也对应的从八次方变成了四次方<br></br>
![example2](https://cloud.githubusercontent.com/assets/22719868/19885408/5f18fc9a-a059-11e6-901f-83101b1f0c45.png)
##实验感想
&emsp;&emsp;这次实验我们运行了两个DOL的小实例，初步理解了DOL的运行过程以及一个DOL程序的写法。通过修改代码，更是加深了我对DOL框架的把握。我觉得确定好一个好的DOL框架，往往比死扣其中的代码更为重要。当然，实验中也遇到些问题。<br></br>
&emsp;&emsp;当然了，不是代码上的问题，代码还是比较简单的。我遇到的问题是跑不出结果，后来发现是因为我只删了之前的dot图，没有把build中的整个exmaple1的结果删掉，删干净后再执行就跑出了正确的结果。<br></br>
&emsp;&emsp;最后才发现，ppt上写了报告不需要写太多，TA大大辛苦了，我下次改进，更详略得当点。
