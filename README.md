#DOL配置实验
***

<h2 align = "center">配置开始</h2>
##一、前期准备

1. 将下图两个压缩包复制到ubantu中<br>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/yasuobao.png"/>
2. 更换软件源，把US服务器改成china中的服务器
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/newsource.png"/>
3. 输入sudo apt-get update, 对ubantu进行更新同步

##二、安装必要软件
<ol>
<li>输入sudo apt-get install ant命令安装ant</li>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/ant.png"/>
<li>输入sudo apt-get install openjdk-7-jdk命令安装jdk</li>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/openjdk.png"/>
<li>输入sudo apt-get install unzip命令安装unzip</li>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/unzip.png"/>
</ol>
##三、解压dol_ethz和systemc-2.3.1
<ol>
<li>输入sudo mkdir dol，新建一个dol文件夹</li>
<li>然后，利用unzip命令sudo unzip dol_ethz.zip -d dol，将dol_ethz解压到上述压缩包中</li>
<li>使用tar命令sudo tar -zxvf systemc-2.3.1.tgz，将systemc-2.3.1解压</li>
</ol>
##四、编译systemc
<ol>
<li>输入cd systemc-2.3.1进入systemc-2.3.1文件夹里面</li>
<li>输入sudo mkdir objdir在其中新建一个objdir的文件夹</li>
<li>输入cd objdir进入上述文件夹</li>
<li>输入sudo ../configure CXX=g++ --disable-async-updates进行配置，便于编译</li>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/configure.png"/>
<li>输入sudo make install进行编译</li>
<li>编译完之后，输入cd ..退回systemc-2.3.1文件夹，再输入ls命令列出其中内容</li>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/ls.png"/>
<li>输入sudo pwd记录当前路径</li>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/pwd.png"/>
</ol>
##五、编译dol
<ol>
<li>输入cd ../dol退回home，进入dol文件夹</li>
<li>利用gedit命令打开build_zip.xml的图形化界面，找到其中如下图所示的语句。原本红框部分是其它路径，现改为上一步pwd所得到的路径。又由于我是64位虚拟机，我又加了个linux64上去</li>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/after.png">
<li>输入sudo ant -f build_zip.xml all进行编译，编译成功之后会看到下图结果</li>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/build.png">
<li>最后，输入cd build/bin/main进入该文件夹，输入sudo ant -f runexample.xml -Dnumber=1运行其中一个例子之后，结果如下，则说明配置实验成功。</li>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/running.png">
</ol>
***
<h2 align = "center">配置完成</h2>
#DOL框架描述（待后续实验完善）
***
&#160;&#160;&#160;&#160;DOL（分布式操作层）是一个框架，它能够使应用程序（半）自动映射到多处理器SHAPES架构平台。
DOL允许指定基于KPN模型计算的应用程序，并且能够设定一个基于SystemC的模拟引擎。
&#160;&#160;&#160;&#160;同时，DOL提供了一个基于XML的规范格式，来描述在一个多处理器系统中并行应用程序的执行，包括绑定和映射操作。 

#实验感想
***
&#160;&#160;&#160;&#160;这次实验中我遇到了一些问题，主要如下：<br>
&#160;&#160;&#160;&#160;1. 在ubuntu下安装、更新必要的环境速度太慢。<br>
&#160;&#160;&#160;&#160;解决办法：当下载速度达到几B每秒的时候，我不禁注意到了下载的源。一看，是个来自美国的莫名其妙的源（因为里面我只认得US这一个单词）。然后我就到网上搜索换源的办法和哪些源用起来速度比较快。后来我就讲源换成了[中科大的镜像源](http://mirrors.ustc.edu.cn/ubuntu)，然后下载速度就飞升了，便解决了问题。<br>
&#160;&#160;&#160;&#160;2. 还有个问题就是各种搞错文件路径。比如还没cd ..回去就开始乱安装，还没有cd进文件夹就开始乱编译，导致各种错误。可以能是开始太着急了，后来调整下心态，慢慢搞就好了。<br>
&#160;&#160;&#160;&#160;3.开始实验文件夹中TA给的dol和systemc的安装包怎么都复制不到我的虚拟机中去。<br>
&#160;&#160;&#160;&#160;解决办法：上网搜索相应的问题。事实上我在去年操作系统的实验文档中找到了相应的解决办法，其实就是安装相应的VMware Tools。不过我照着这样做了之后依然没有用，于是我就换了几个教程，依然没什么用。室友建议我用共享内存大法，但是我觉得这样有些麻烦。然后，一气之下我重装了虚拟机。<br>
&#160;&#160;&#160;&#160;在重装虚拟机的时候我大概发现了问题（应该是）：我开始创虚拟机的时候选择的是“经典模式”；顾名思义，就是只有基本功能，其他的一些配置都给你搞的差不多了的那种。后来我选择了“自定义高级模式”，然后安装完成后就正常了，可以自由和主机进行文件互传。所以，我怀疑是不是“经典模式”会缺少一些功能或接口，导致不能互传文件，而且装了VMware Tools都不行。<br>
&#160;&#160;&#160;&#160;虽然有很多意外，但个人觉得自己这次实验中运气还是比较好的。除了两个地方没cd到指定文件夹，其他的都是直接success的。TA大大可能会发现截图中很多安装结果都显示“你已安装”或者“0更新”之类的，那是因为我第一次安装时没截图，这次是重新安装。<br>
&#160;&#160;&#160;&#160;不管怎样，第一次嵌入式实验算是完成了，有疑惑也有成就，期待着下次实验。<br>

