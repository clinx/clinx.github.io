---
layout: post
title: java线程之并发编程
description: 线程本身的使用是简单的，但如果设计到多线程对资源的共享，线程的执行顺序（公平性），那么线程的安全就可能出现漏洞。
category: java
---
	
线程对异步工作流转换为顺序工作流是相当容易的，而且在提高cpu的利用率上也是很nice的。能对2个或多个任务同时工作比较容易给出合理的实现。如
用户提交文件button点击，这个时候就可以分两个任务进行处理，1.渲染点击后button的UI如颜色，2.发送要提交的类容到服务器。但有时后线程之间会
共享一部分资源，而且这些资源是有状态集合的，这样造成不同线程对资源访问的竞争条件（不同的顺序对其访问），可见性（得到资源，可能在另一个线程
已经修改）上出现安全问题。  
	
那么在多线程中那些是怎么分辨需要同步的数据呢。首先线程栈中的数据不需要，比如在栈帧（java 中的方法）中的new的对象，这个引用不会出现在别的线程
中，当方法执行完闭，即可以对其销毁。其次是对共享进行拷贝，使用ThreadLocal这个在JDBC中可以看到。比如对connection使用ThreadLocal进行获取，这样
保证在多线程中其中一个线程对connection进行关闭，而另一个线程却执行查询造成的错误(事务是数据库支持的，不是jdbc实现的。所以session不是
transaction的最小操作元).然后是final的Primitive类型数据是线程安全的，但如果是Object型的对象及时是final的也不一定是同步的，因为对象不可变
和对象引用不可变不等同。而volatile只是可见性上（）是线程安全的（这种数据不会保持在寄存器或高级缓存中，程序可以直接读取到数据的最新值，意味着读
是一个原子操作）。  

Java中对共享对象实现同步分两种：  
1.Synchronized 用同步代码块，如果有多个方法访问入口是需配合信号互斥量（wait）共同使用.Synchronized(lock)  
2.ReentrantLock可以使用它创建一个公平锁或者不公平锁。具有可伸缩性控制（时间锁等候、可中断锁等候、无块结构锁、多个条件变量或者锁投票），性能会更好。但不能忘了finally加unlock. Lock lock = new ReentrantLock().而且有一个特定的读写锁。  
<pre>
public class Account {
	private int balance = 1000;
	public void deposit(int amount){
		balance += amount;
	}
	public void withdraw(int amount){
		balance -= amount;
	}
	public int getBalance(){
		return balance;
	}
	public static void transfer(Account acc1, Account acc2, int amount){
		acc1.withdraw(amount);
		acc2.deposit(amount);
	}
}	
</pre>
<pre>
public class App {
	public static void main(String[] args) throws InterruptedException{
		final Runner runner =new Runner();
		Thread t1 =new Thread(){
			public void run(){
					try {
						runner.firstThread();
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
			}
		};

		Thread t2 =new Thread(){
			public void run(){
					try {
						runner.secondThread();
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
			}
		};
		
		t1.start();
		t2.start();
		t1.join();
		t2.join();
		runner.finished();
	}
	
}
</pre>
<pre>
import java.util.Random;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Runner {

	private Account acc1 = new Account();
	private Account acc2 = new Account();
	private Lock lock1 = new ReentrantLock();
	private Lock lock2 = new ReentrantLock();
	
	private void acquireLocks(Lock firstLock, Lock secondLock) throws InterruptedException{
		while(true){
			// acquire locks
			boolean gotFirstLock = false;
			boolean gotSecondLock = false;
			try{
				gotFirstLock = firstLock.tryLock();
				gotSecondLock = secondLock.tryLock();
			}finally{
				if(gotFirstLock && gotSecondLock){
					return;
				}
				if(gotFirstLock){
					firstLock.unlock();
				}
				if(gotSecondLock){
					secondLock.unlock();
				}
				
			}
			Thread.sleep(1);
		}
	}
	
	public void firstThread() throws InterruptedException{
		Random random = new Random();
		for(int i=0; i<1000; i++){
			acquireLocks(lock1,lock2);
			try{
				Account.transfer(acc1, acc2, random.nextInt(1000));
			}finally{
				lock2.unlock();
				lock1.unlock();
			}
			
		}
	}
	
	public void secondThread() throws InterruptedException{
		Random random = new Random();
		for(int i=0; i<1000; i++){
			acquireLocks(lock2,lock1);
			try{
				Account.transfer(acc2, acc1, random.nextInt(1000));
			}finally{
				lock2.unlock();
				lock1.unlock();
			}
			
		}
	}
	
	public void finished(){
		System.out.println("Account 1 balance: " + acc1.getBalance());
		System.out.println("Account 2 balance: " + acc2.getBalance());
		System.out.println("Total balance: " + (acc1.getBalance() + acc2.getBalance()));
	}
}
</pre>

其他[同步demo code](https://github.com/clinx/ConcurrencyThreadDemo) 

##参考质料  
JAVA并发编程实践 
[Cave of programming](http://www.caveofprogramming.com/)  




