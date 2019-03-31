---
layout: post
comments: true
categories: 多线程
---

# 带for循环代码的线程被interrupt后，依然会执行完for循环后代码的问题解决

当试图停止一个带有for循环的线程时，会出现如下“不想出现的情况”。


线程类：


```
public class ThreadInterruptTest1 extends Thread{
	public void run(){
		try {
			for(int i=0;i<500000;i++){
				if(this.interrupted()){
					System.out.println("退出了");
					break;
				}
				System.out.println(i);
			}
			System.out.println("因为是在for循环之后，所以又执行了该段语句，该线程并未停止");
			this.sleep(3000);
			System.out.println("因为是在for循环之后，所以又执行了该段语句，该线程还未停止");
			this.sleep(6000);
			System.out.println("因为是在for循环之后，所以又执行了该段语句，该线程依然未停止");
		} 
		catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}
```

执行结果：

```
152990
152991
152992
152993
152994
152995
152996
152997
152998
152999
退出了
因为是在for循环之后，所以又执行了该段语句，该线程并未停止
因为是在for循环之后，所以又执行了该段语句，该线程还未停止
因为是在for循环之后，所以又执行了该段语句，该线程依然未停止
```

这不是程序员想要的结果，正常情况下既然已经将线程interrupt，并且也已经通过interrupted()方法测试线程已经是被停止了，但却依然将for循环外剩余的代码执行完毕，线程会将这段代码执行完毕后才真正停止。


避免这种情况的方法是用“异常法”。

线程类：
```
public class ThreadInterruptTest extends Thread{
	public void run(){
		try {
			for(int i=0;i<500000;i++){
				if(this.interrupted()){
					System.out.println("准备退出");
					throw new InterruptedException();
				}
				System.out.println(i);
			}
			System.out.println("因为是在for循环之后，所以又执行了该段语句，该线程并未停止吗?");
		} 
		catch (InterruptedException e) {
			//e.printStackTrace();
			System.out.println("退出成功了");
		}
	}
}
```
执行类：
```
public class Main {
	public static void main(String[] args) {
		try {
			ThreadInterruptTest tit = new ThreadInterruptTest();
//			ThreadInterruptTest1 tit = new ThreadInterruptTest1();
			tit.start();
			Thread.sleep(1000);
			tit.interrupt();
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}
```
执行结果：
```
147036
147037
147038
147039
147040
147041
147042
147043
147044
147045
147046
准备退出
退出成功了
```
可以看到线程interrupt后直接进catch了，for循环外的print代码没有执行。