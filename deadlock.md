
#实验三：死锁
***
###一、实验目的
&emsp;&emsp;本次实验目的是让我们体验一个简单的生成死锁的java程序，探究其为什么可以产生死锁，并复习死锁产生的四个必要条件。
###二、背景知识
&emsp;&emsp;因为本次的java函数中都用了sychronized修饰，所以在理解死锁之前我们要先搞懂sychronized方法的用法，同步方法和同步对象不一样，同步对象是sychronized(obj) 这样一来，谁被选中谁就有锁。
&emsp;&emsp;而同步函数是锁定的是调用它的线程,而调用的对象就是同步锁，直到执行完整个同步函数之后，锁才会被释放，在有锁的过程中，该对象所处同步代码块无法被别的线程或者进程调用，若是调用就会导致调用它的对象被阻塞。
###三、实验分析
* 产生死锁的代码如下：

```java
class A {
	synchronized void methodA(B b) {
		b.last();
	}

	synchronized void last() {
		System.out.println("Inside A.last()");
	}
}

class B {
	synchronized void methodB(A a) {
		a.last();
	}

	synchronized void last() {
		System.out.println("Inside B.last()");
	}
}

class Deadlock implements Runnable{
	A a = new A();
	B b = new B();

	Deadlock(){
		Thread t = new Thread(this);
		int count = 10000;
		t.start();
		while(count-->0);
		a.methodA(b);
	}

	public void run(){
		b.methodB(a);
	}
	public static void main(String args[]){
		new Deadlock();
	}
}
```
1. 对A类和B类的理解。
    &emsp;&emsp;A类和B类里面都有两个synchronized函数，A中的method函数用于调用B类中的输出函数last()，B中的method函数用于调用A类中的输出函数last()。
    
2. 对runnable函数的理解。
&emsp;&emsp;Deadlock类中，首先创建了A，B两个类。
&emsp;&emsp;然后在main函数中new一个Deadlock类，此时必然会调用Deadlock中重载的构造函数，在这个函数中，又创建了另一个线程t，这个线程就是个搅屎棍，它存在的目的就是为了提供产生死锁的前提条件的。当把它start的时候，它就开始被调度去跑run()函数里的内容。
&emsp;&emsp;然后设置构造函数自旋的时间（一个while循环），调整合适的时间，使得线程t就被调用后运行run函数的时候，主函数刚好执行a.methodA(b),以发生死锁。

###四、实验结果（死锁产生在第几次的图）

&emsp;&emsp;![deadlock1](https://cloud.githubusercontent.com/assets/22719868/19889608/bab3a53e-a070-11e6-991a-db1d65cf1d49.png)
&emsp;&emsp;  ` 如图所示，第6次执行java文件时发生死锁，此时count我调整了10000.`

###五、产生死锁的四个必要条件
参考[某大神csdn博客](http://blog.csdn.net/hijiankang/article/details/9157365)以及[饶阳辉老师操作系统课件](http://smie2.sysu.edu.cn/~ryh/os/file/presentation/ch7.pdf)

**1. 互斥条件(Mutual exclusion):**
&emsp;&emsp;即某个资源在一段时间内只能由一个进程占有，不能同时被两个或两个以上的进程占有。这种独占资源如驱动器，打印机等等，必须在占有该资源的进程主动释放它之后，其它进程才能占有该资源。。如独木桥就是一种独占资源，两方的人不能同时过桥。
**2. 不可抢占条件(No preemption):**
&emsp;&emsp;进程所获得的资源在未使用完毕之前，资源申请者不能强行地从资源占有者手中夺取资源，而只能由该资源的占有者进程自行释放。如过独木桥的人不能强迫对方后退，也不能非法地将对方推下桥，必须是桥上的人自己过桥后空出桥面（即主动释放占有资源），对方的人才能过桥。
**3. 占有且申请条件(Hold and wait):**
&emsp;&emsp;进程至少已经占有一个资源，但又申请新的资源；由于该资源已被另外进程占有，此时该进程阻塞；但是，它在等待新资源之时，仍继续占用已占有的资源。还以过独木桥为例，甲乙两人在桥上相遇。甲走过一段桥面（即占有了一些资源），还需要走其余的桥面（申请新的资源），但那部分桥面被乙占有（乙走过一段桥面）。甲过不去，前进不能，又不后退；乙也处于同样的状况。
**4. 循环等待条件(Circular wait):**
&emsp;&emsp;存在一个进程等待序列{P1，P2，...，Pn}，其中P1等待P2所占有的某一资源，P2等待P3所占有的某一源，......，而Pn等待P1所占有的的某一资源，形成一个进程循环等待环。就像前面的过独木桥问题，甲等待乙占有的桥面，而乙又等待甲占有的桥面，从而彼此循环等待。  

* 上述四个条件在死锁时会同时发生。也就是说，只要有一个必要条件不满足，则死锁就可以排除。

###六、对本次实验程序产生死锁的解释

&emsp;&emsp; 1. 互斥条件：因为A类和B类中的所有函数都有sychronized修饰，所以同一时刻只能有一个线程去调用A的对象或B的对象，这是互斥条件。
&emsp;&emsp; 2. 非抢占条件：代码中并没有任何抢占的操作，所以必须当一个线程释放了它所占有的对象之后，另一个线程才能够去访问。
&emsp;&emsp; **3. 占有并申请**： 这是这次实验的重点，经过我们调count值，会出现这种情况：主线程刚执行a.methodA(b)的时候（此时主线程占有a并申请b），刚好子线程执行了b.methodB(a)（此时子线程占有了b并申请a）。
&emsp;&emsp;4. 循环等待条件：此时刚好主线程占有a资源，但如果想继续运行就得得到b资源；子线程占有b资源，但如果想要继续运行就得得到a资源。
>&emsp;&emsp;关于为什么主线程执行while循环后，子线程可能抢占cup资源并执行，我的想法是这是个“多级反馈调度队列”的机制，某个线程执行的时间越长，该线程的优先级就越低，而优先级变低的时候就会发生抢占。
&emsp;&emsp;所以，当count值设置合适时，methodA()运行到一半优先级就变得比线程t低了，此时线程t就会调用了run函数，于是就会执行语句b.methodB(a),而这个函数中需要调用a.last()函数，由于a还未执行完methodA(),所以Deadlock的锁还未释放，因此methodB()无法完成，同时线程t也被阻塞。这种情况就会发生死锁。