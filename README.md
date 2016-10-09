#DOL配置实验
***
<h2 align = "center">配置开始</h2>
##一、前期准备
<ol>
<li>将下图两个压缩包复制到ubantu中</li>
![yasuobao]("https://github.com/XueboShan/pictures/blob/master/emlab1/yasuobao.png")
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/yasuobao.png"/>
<li>更换软件源，把US服务器改成china中的服务器</li>
<img src="https://github.com/XueboShan/pictures/blob/master/emlab1/newsource.png"/>
<li>输入sudo apt-get update, 对ubantu进行更新同步</li>
</ol>
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
<h2 align = "center">配置完成</p>
#DOL框架描述（待后续实验完善）
***
&#160; &#160; &#160; &#160;DOL（分布式操作层）是一个框架，它能够使应用程序（半）自动映射到多处理器SHAPES架构平台。
DOL允许指定基于KPN模型计算的应用程序，并且能够设定一个基于SystemC的模拟引擎。
同时，DOL提供了一个基于XML的规范格式，来描述在一个多处理器系统中并行应用程序的执行，包括绑定和映射操作。 

#实验感想
***
