#实验五：安装ROS
***
###1. 添加源
&emsp;&emsp;打开一个控制台（Ctrl+Alt+T),输入如下指令：
```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```
&emsp;&emsp;然后再设置秘钥：
```
sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 0xB01FA116
```
运行结果：
![key](https://cloud.githubusercontent.com/assets/22719868/20179658/3723103c-a792-11e6-8115-172701cd22ed.png)
###2. 安装ROS
&emsp;&emsp;首先确保系统软件处于最新版。
```
sudo apt-get update
```
运行结果：
![update](https://cloud.githubusercontent.com/assets/22719868/20179662/37547a96-a792-11e6-95f5-2a5b7cee3740.png)
&emsp;&emsp;然后我们就可以安装 ROS 了，然而ROS kinetic 有很多版本，比如工业版，基础版，高级版，豪华版，至尊豪华版，我在毫不知情的情况下安装了至尊豪华版：
```
sudo apt-get install ros-kinetic-desktop-full
```
运行结果：
![installfull](https://cloud.githubusercontent.com/assets/22719868/20179657/3722d4c8-a792-11e6-8d6a-71f253954394.png)
&emsp;&emsp;安装完成后，我们可以使用以下命令来查看我们课使用的包：
```
apt-cache search ros-kinetic
```
运行结果：
![search](https://cloud.githubusercontent.com/assets/22719868/20179661/37516ef0-a792-11e6-9115-c7a6958b948c.png)
###3.初始化ROS
* 首先需要初始化rosdep，具体如下：
```
sudo rosdep init
rosdep update
```
运行结果：
![rosdepupdate](https://cloud.githubusercontent.com/assets/22719868/20179660/37414c82-a792-11e6-99e6-530c91b6ff04.png)

*  然后去初始化环境变量：
```
echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
> 上面两句非常重要，很多人在日常的开发过程中，有的找不到 Package, 找不到node, 很多情况下都是没有添加source的原因。

*  最后我们再来安装一个非常常用的插件，它使得我们可以更容易地下载ROS包的一些资源树：
```
sudo apt-get install python-rosinstall
```
运行结果：
![final2](https://cloud.githubusercontent.com/assets/22719868/20179655/36f3f7ac-a792-11e6-9efb-6057cdae0761.png)

#拓展部分：体验cartography
***
###1. 安装所有依赖项
```
sudo apt-get install -y google-mock libboost-all-dev  libeigen3-dev libgflags-dev libgoogle-glog-dev liblua5.2-dev libprotobuf-dev  libsuitesparse-dev libwebp-dev ninja-build protobuf-compiler python-sphinx  ros-indigo-tf2-eigen libatlas-base-dev libsuitesparse-dev liblapack-dev
```
很惭愧，有一个微小的依赖项安装不了，不过不影响。
![yilaixiang](https://cloud.githubusercontent.com/assets/22719868/20214582/19ab4732-a84a-11e6-9230-378c23f2f7d0.png)
###2.安装ceres solver，选择的版本是1.11
```
git clone https://github.com/hitcm/cartographer.git
cd cartographer/build
cmake .. -G Ninja
ninja
ninja test
sudo ninja install
```
我的执行结果如下：
![attempt](https://cloud.githubusercontent.com/assets/22719868/20214714/c1385706-a84a-11e6-9fe2-a0acc1c9f432.png)
（显然有问题）
###3.安装 cartographer
```
cmake  ..
make
sudo make install
```
* 这一步显然和上一步结果差不多，于是我把室友下载的cartographer和ceres-solver-1.11.0包拷过来继续实验，结果显然又失败了。
*  无奈之下，我又开了台虚拟机，重新安装ROS，然后再安装cartographer，运行结果如下：

![failedagain](https://cloud.githubusercontent.com/assets/22719868/20214791/3ec03b62-a84b-11e6-9ee3-2919f1522973.png)

*  上图可以看出我虚拟机的主机名和用户名都不一样了，证明我之前没有吹水。

###4.好像出了点问题。
* 但是，尽管历经千辛万苦，经过了半天一夜的奋斗，还是无法把官方给的2D和3D 的效果跑出来。我觉得可能是自己ubuntu的版本是15.10的问题。

###5.换种体验方法
* 但是，这个实验的目的不就是让我们去体验下cartographer嘛，我们没必要就跑那个2D和3D的数据。
* 于是我从网上找了个控制小乌龟跑的例子体验下，也算勉强完成了实验了（值得一提的是，此时我又换了台虚拟机，貌似前面两台被我玩hadoop玩得ping不了自己了，不能搞cartographer了）。
* 实验结果：

![success](https://cloud.githubusercontent.com/assets/22719868/20214938/3290dc60-a84c-11e6-972c-f8e56b2162eb.png)
###tips：
* 第一个terminal是开启模式。
* 第二个terminal是开启界面并追踪绘画出路径。
* 第三个terminal是设置通过方向键来控制机器人。
*  这次实验真是费了很多时间，换了虚拟机也翻了墙，不过都没什么用，但是最后这个小机器人跑出来还是挺开心的，略微体验到了Ros的功能，这次实验就这样了，希望下次实验能做得更好。
