# Thinking in java 多线程学习笔记
标签（空格分隔）： java多线程
---

##并发的意义
- 并发是用于多处理器编程的基本工具。
- 但通常并发用于处理单处理器上的程序的性能，如果没有并发，io可能会将任务阻塞，以至于其他线程都需要等待
- 使用并发可以产生具有可响应的用户界面

##Thread和Runnable接口的基本使用
线程调度如果不加锁，输出结果会有错误
```java
class useThread extends Thread{
    private static int ticket =100;
    private boolean flag = true;
    public  void run(){
        while (flag)
        {

            if(ticket>0)
            {
                System.out.println(Thread.currentThread().getName()+"剩余票数\t"+ticket);
                ticket--;
            }
            else
            {
               flag=false;
            }

        }

    }
}
```
执行结果
```
……
pool-1-thread-1售出当前票号	3
pool-1-thread-2售出当前票号	4
pool-1-thread-4售出当前票号	1
pool-1-thread-1售出当前票号	1
pool-1-thread-3售出当前票号	2
```

```java
import java.util.concurrent.Executor;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * Created by kx on 2015/10/1.
 */
//开4个窗口同时售票
public class ThreadBasic {
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(4);
        for(int i=0;i<5;i++)
        {
            executorService.execute(new useThread());
        }
    }
}
class useThread extends Thread{
    private static int ticket =100;
    private boolean flag = true;
    public  void run(){
        while (flag)
        {

            if(ticket>0)
            {
                synchronized (ThreadBasic.class)
                {
                    if (ticket>0)
                    {
                        System.out.println(Thread.currentThread().getName()+"售出当前票号\t"+ticket);
                        ticket--;
                        Thread.yield();
                    }
                    else {
                        flag=false;
                    }
                }

            }
            else
            {
               flag=false;
            }

        }

    }
}

```
执行结果
```
pool-1-thread-2售出当前票号	7
pool-1-thread-2售出当前票号	6
pool-1-thread-1售出当前票号	5
pool-1-thread-1售出当前票号	4
pool-1-thread-1售出当前票号	3
pool-1-thread-1售出当前票号	2
pool-1-thread-1售出当前票号	1
```

仍旧存在的问题，虽然加锁后线程可以执行并按照预想的结果顺序卖票，但是线程不会跳出循环，不可以自动退出。

> executorService.shutdown();

所以用完线程池后一定要记着关闭，不然线程并不会关闭，还会占用计算机资源。