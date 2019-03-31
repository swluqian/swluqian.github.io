---
layout: post
comments: true
categories: 多线程
---

# Thread与Runnable区别

执行多线程操作可以选择
- 继承Thread类
- 实现Runnable接口

#### 1.继承Thread类
以卖票窗口举例，一共5张票，由3个窗口进行售卖(3个线程)。


```
package thread;
public class ThreadTest {
	public static void main(String[] args) {
		MyThreadTest mt1 = new MyThreadTest("窗口1");
		MyThreadTest mt2 = new MyThreadTest("窗口2");
		MyThreadTest mt3 = new MyThreadTest("窗口3");
		mt1.start();
		mt2.start();
		mt3.start();
	}
}
class MyThreadTest extends Thread{
	private int ticket = 5;    
	private String name;
	public MyThreadTest(String name){
		this.name = name;
	}
    public void run(){    
         while(true){  
             if(ticket < 1){    
                break;  
             }   
             System.out.println(name + " = " + ticket--);    
         }    
    } 
}
```

执行结果：

```
窗口1 = 5
窗口1 = 4
窗口1 = 3
窗口1 = 2
窗口1 = 1
窗口2 = 5
窗口3 = 5
窗口2 = 4
窗口3 = 4
窗口3 = 3
窗口3 = 2
窗口3 = 1
窗口2 = 3
窗口2 = 2
窗口2 = 1
```

结果一共卖出了5*3=15张票，这违背了"5张票"的初衷。
造成此现象的原因就是：

```
MyThreadTest mt1 = new MyThreadTest("窗口1");
		MyThreadTest mt2 = new MyThreadTest("窗口2");
		MyThreadTest mt3 = new MyThreadTest("窗口3");
		mt1.start();
		mt2.start();
		mt3.start();
```

一共创建了3个MyThreadTest对象，而这3个对象的资源不是共享的，即各自定义的ticket=5是不会共享的，因此3个线程都执行了5次循环操作。

#### 2.实现Runnable接口
同样的例子

```
package thread;
public class RunnableTest {
	public static void main(String[] args) {
		MyRunnableTest mt = new MyRunnableTest();
		Thread mt1 = new Thread(mt,"窗口1");
		Thread mt2 = new Thread(mt,"窗口2");
		Thread mt3 = new Thread(mt,"窗口3");
		mt1.start();
		mt2.start();
		mt3.start();
	}
}
class MyRunnableTest implements Runnable{
	private int ticket = 5;    
	public void run(){    
         while(true){  
    		 if(ticket < 1){    
    			 break;  
    		 }    
    		 System.out.println(Thread.currentThread().getName() + " = " + ticket--);    
         }    
    } 
}
```

结果：

```
窗口1 = 5
窗口1 = 2
窗口3 = 4
窗口2 = 3
窗口1 = 1
```

结果卖出了预期的5张票。
原因在于：

```
MyRunnableTest mt = new MyRunnableTest();
		Thread mt1 = new Thread(mt,"窗口1");
		Thread mt2 = new Thread(mt,"窗口2");
		Thread mt3 = new Thread(mt,"窗口3");
		mt1.start();
		mt2.start();
		mt3.start();
```

只创建了一个MyRunnableTest对象，而3个Thread线程都以同一个MyRunnableTest来启动，所以他们的资源是共享的。
