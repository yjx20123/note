##### 1：什么是JUC

##### 2:线程和进程

>线程有几个状态

```java
 public enum State {
      //新生
        NEW,

        //运行
        RUNNABLE,

        //阻塞
        BLOCKED,

       //等待，死死的等
        WAITING,

        //超时等待
        TIMED_WAITING,

       //终止等待
        TERMINATED;
    }
```



> wait/sleep区别

**1、来自不同的类**

wait=>Object

sleep=>Thread 

**2、关于锁的释放**

wait会释放锁，sleep睡觉了，抱着锁睡觉，不会释放

**3、使用的范围是不同的**

wait

wait不许再同步代码块中

sleep可以在任何地方睡

**4、是否需要捕获异常**

wait不需要捕获异常

 sleep必须要捕获异常

##### 3:Lock锁（重点）

```java
public class SaleTicketDemo01 {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();
        new Thread(()->{
            for (int i = 0; i < 60; i++) {
                ticket.sale();
            }
        },"a").start();new Thread(()->{
            for (int i = 0; i < 60; i++) {
                ticket.sale();
            }
        },"b").start();new Thread(()->{
            for (int i = 0; i < 60; i++) {
                ticket.sale();
            }
        },"c").start();
    }
}
class Ticket{
    //属性，方法
    private int number=50;
    //买票的方式
    public synchronized void sale(){
        if(number>0){
            System.out.println(Thread.currentThread().getName()+number--+"票");
        }
    }
}


```

![image-20211027141709560](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211027141709560.png)

![image-20211027141720025](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211027141720025.png)

公平锁：十分公平，可以先来后到

**非公平锁：十分不公平，可以插队（默认 ）**

```java
import sun.awt.Mutex;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class SaleTicketDemo01 {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();
        new Thread(()->{for (int i = 0; i < 60; i++)ticket.sale();},"a").start();
        new Thread(()->{
            for (int i = 0; i < 60; i++) {
                ticket.sale();
            }
        },"b").start();new Thread(()->{
            for (int i = 0; i < 60; i++) {
                ticket.sale();
            }
        },"c").start();
    }
}
class Ticket{
    //属性，方法
    private int number=50;
    Lock lock=new ReentrantLock();
    //买票的方式
    public  void sale(){

        lock.lock();//加锁
        lock.tryLock();
       try {
           if(number>0){
               System.out.println(Thread.currentThread().getName()+number--+"票");
           }
       } catch (Exception e) {
           e.printStackTrace();
       }finally {
        lock.unlock();//解锁
       }
    }
}


```

> Synchronized 和Lock 区别

1. Synchronized 内置的Java关键字，Lock是一个Java类
2. Synchronized 无法判断获取锁的状态，Lock 可以判断是否获取到了锁
3. Synchronized 会自动释放锁，lock必须要手动释放锁！如果不释放锁，死锁
4. Synchronized 线程1（获得锁，阻塞）、线程2（等待，傻傻的等），Lock锁就不一定会等待下去；
5. Synchronized 可重入锁，不可以中断的，非公平，Lock，可重入锁，可以判断锁，非公平（可以自己设置）
6. Synchronized 适合锁少量的代码同步问题，Lock适合锁大量的同步代码

> 锁是什么，如何判断锁的是谁

##### 4、生产者和消费者问题

面试的：单例模式、排序算法、生产者和消费者、死锁

> 生产者和消费者Synchronized版

```java
package 生产者和消费者问题;

/**
 * 线程之间的通信问题，生产者和消费者问题！等待唤醒，通知唤醒
 * 线程交替执行 A B 操作同一个变量 num = 0
 * A num+1
 * B num-1
 */
public class test1 {
    public static void main(String[] args) {
        Data data = new Data();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A").start();
        new Thread(()->{ for (int i = 0; i < 10; i++) {
            try {
                data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }},"B").start();
    }
}
//等待，业务，通知
class Data{
    //数字，资源
    private int number=0;
    public synchronized void increment() throws InterruptedException {
        if(number!=0){
            //等待
            this.wait();
        }
        number++;
        System.out.println(Thread.currentThread().getName()+"==>"+number);
        this.notifyAll();
        //通知其他线程，我+1完毕了

    }
    public synchronized void decrement() throws InterruptedException {
        if(number==0){
            //等待

            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName()+"==>"+number);
        this.notifyAll();
        //通知其他线程，我-1完毕了

    }
}
```

> 问题存在，A B C D 同时存在

```java
package 生产者和消费者问题;

/**
 * 线程之间的通信问题，生产者和消费者问题！等待唤醒，通知唤醒
 * 线程交替执行 A B 操作同一个变量 num = 0
 * A num+1
 * B num-1
 */
public class test1 {
    public static void main(String[] args) {
        Data data = new Data();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A").start();
        new Thread(()->{ for (int i = 0; i < 10; i++) {
            try {
                data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }},"B").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"C").start();
        new Thread(()->{ for (int i = 0; i < 10; i++) {
            try {
                data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }},"D").start();
    }

}
//等待，业务，通知
class Data{
    //数字，资源
    private int number=0;
    public synchronized void increment() throws InterruptedException {
        while (number!=0){
            //等待
            this.wait();
        }
        number++;
        System.out.println(Thread.currentThread().getName()+"==>"+number);
        this.notifyAll();
        //通知其他线程，我+1完毕了

    }
    public synchronized void decrement() throws InterruptedException {
        while (number==0){
            //等待

            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName()+"==>"+number);
        this.notifyAll();
        //通知其他线程，我-1完毕了

    }
}
```

> JUC版的生产者和消费者问题

```java
package 生产者和消费者问题;

/**
 * 线程之间的通信问题，生产者和消费者问题！等待唤醒，通知唤醒
 * 线程交替执行 A B 操作同一个变量 num = 0
 * A num+1
 * B num-1
 */
public class test1 {
    public static void main(String[] args) {
        Data data = new Data();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A").start();
        new Thread(()->{ for (int i = 0; i < 10; i++) {
            try {
                data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }},"B").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"C").start();
        new Thread(()->{ for (int i = 0; i < 10; i++) {
            try {
                data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }},"D").start();
    }

}
//等待，业务，通知
class Data{
    //数字，资源
    private int number=0;
    public synchronized void increment() throws InterruptedException {
        while (number!=0){
            //等待
            this.wait();
        }
        number++;
        System.out.println(Thread.currentThread().getName()+"==>"+number);
        this.notifyAll();
        //通知其他线程，我+1完毕了

    }
    public synchronized void decrement() throws InterruptedException {
        while (number==0){
            //等待

            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName()+"==>"+number);
        this.notifyAll();
        //通知其他线程，我-1完毕了

    }
}
```



通过Lock实现Contain![image-20211028112345283](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211028112345283.png)

> Condition

```java
package 生产者和消费者问题;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

//Condition
public class Test2 {
    public static void main(String[] args) {
        Data2 data = new Data2();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    data. increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A").start();
        new Thread(()->{ for (int i = 0; i < 10; i++) {
            try {
                data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }},"B").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"C").start();
        new Thread(()->{ for (int i = 0; i < 10; i++) {
            try {
                data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }},"D").start();
    }

}
//等待，业务，通知
class Data2{
    //数字，资源
    private int number=0;
    Lock lock=new ReentrantLock();
    Condition condition = lock.newCondition();
    public void increment() throws InterruptedException {
        lock.lock();
       try {
           while (number!=0){
               //等待
               condition.await();
           }
           number++;
           System.out.println(Thread.currentThread().getName()+"==>"+number);
           condition.signalAll();
           //通知其他线程，我+1完毕了
       } catch (InterruptedException e) {
           e.printStackTrace();
       }finally {
           lock.unlock();
       }

    }
    public void decrement() throws InterruptedException {
        lock.lock();
       try {
           while (number==0){
               //等待

               condition.await();
           }
           number--;
           System.out.println(Thread.currentThread().getName()+"==>"+number);
           condition.signalAll();
           //通知其他线程，我-1完毕了
       } catch (InterruptedException e) {
           e.printStackTrace();
       }
       finally {
           lock.unlock(); 
       }
    }
}
```

> 轮流执行

```java
package 生产者和消费者问题;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

//A执行完执行B,B执行完执行C,C执行完执行A
public class Test3 {

    public static void main(String[] args) {
        Data3 data3 = new Data3();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                data3.printA();
            }
        },"A").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                data3.printB();
            }
        },"B").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                data3.printC();
            }
        },"C").start();
    }
}

class Data3 {
    private Lock lock = new ReentrantLock();
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();
    private int number=1;
    public void printA() {
        lock.lock();
        try {
            while (number != 1) {
                condition1.await();
            }
            System.out.println(Thread.currentThread().getName()+"AAAAAA");
            number=2;
            condition2.signal();
//业务，判断-》执行——》通知
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void printB() {
        lock.lock();
        try {
//业务，判断-》执行——》通知
            while (number!=2){
                condition2.await();
            }
            System.out.println(Thread.currentThread().getName()+"BBBB");
            number=3;
            condition3.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void printC() {
        lock.lock();
        try {

//业务，判断-》执行——》通知
            while (number!=3){
                condition3.await();
            }
            System.out.println(Thread.currentThread().getName()+"CCCCCC ");
            number=1;
            condition1.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
    //生产线
    //下单——》支付——》交易——》物流
}

```

##### 5、八锁现象

------

如何判断锁的是谁，永远的知道什么是锁，锁到底锁的是谁！

**深刻理解我们的锁**

```java
package Lock8;

import java.util.concurrent.TimeUnit;

/**
 * 8锁，就是关于锁的8个问题
 * 1、标准情况下，两个线程先打印 发短信还是打电话
 */
public class Test1{
    public static void main(String[] args) {
        Phone phone = new Phone();
        Phone phone1 = new Phone();
        //phone.sendSms();
        new Thread(()->{
            phone.sendSms();
        },"A").start();
        try {
            TimeUnit.SECONDS.sleep(1);;
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(()->{
            phone1.call();
        },"B").start();new Thread(()->{
            phone.hello();
        },"B").start();
    }
}
class Phone{
    //synchronized 锁的对象是方法的调用者
    public synchronized void sendSms(){
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("发短信");
    }
    public synchronized void call(){
        System.out.println("打电话");
    }
//    这里；没有锁，不是同步方法，不受锁的影响
    public void hello(){
        System.out.println("hello");
    }
}

```

```java
package Lock8;

import java.util.concurrent.TimeUnit;

/**
 * 8锁，就是关于锁的8个问题
 * 1、标准情况下，两个线程先打印 发短信还是打电话
 */
public class Test1{
    public static void main(String[] args) {
        Phone phone = new Phone();
        Phone phone1 = new Phone();
        //phone.sendSms();
        new Thread(()->{
            phone.sendSms();
        },"A").start();
        try {
            TimeUnit.SECONDS.sleep(1);;
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(()->{
            phone1.call();
        },"B").start();new Thread(()->{
            phone.hello();
        },"B").start();
    }
}
class Phone{
    //synchronized 锁的对象是方法的调用者
    public synchronized void sendSms(){
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("发短信");
    }
    public synchronized void call(){
        System.out.println("打电话");
    }
//    这里；没有锁，不是同步方法，不受锁的影响
    public void hello(){
        System.out.println("hello");
    }
}

```

```java
package Lock8;

import java.util.concurrent.TimeUnit;

public class Test4 {
    public static void main(String[] args) {
        Phone4 phone = new Phone4();
        Phone4 phone1 = new Phone4();
        //phone.sendSms();
        new Thread(() -> {
            phone.sendSms();
        }, "A").start();
        try {
            TimeUnit.SECONDS.sleep(1);
            ;
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(() -> {
            phone1.call();
        }, "B").start();
        new Thread(() -> {
            phone.hello();
        }, "B").start();
    }
}

class Phone4 {
    //synchronized 锁的对象是方法的调用者
    //静态的同步方法，锁的是CLass模板
    public static synchronized void sendSms() {
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("发短信");
    }

    public static synchronized void call() {
        System.out.println("打电话");
    }

    //普通的同步方法 锁的调用者
    public synchronized void hello() {
        System.out.println("hello");
    }
}


```

> 小结

new this 具体的一个手机

static Class唯一的一个模板

##### 6、集合类不安全

---

> List 不安全 

```Java
package unsafe;

import java.util.*;
import java.util.concurrent.CopyOnWriteArrayList;

//java.util.ConcurrentModificationException并发修改异常！！！！！
public class ListTest{
    public static void main(String[] args) {
//        List<String> list= Arrays.asList("1","2","3");
//        list.forEach(System.out::println);
//        List list = new ArrayList<>();
        /**
         * 并发下ArrayList 是不安全的
         * 解决方案
         * 1、vector解决
         *
         */
//     1. List<String> list=new Vector<>();
//     2 .  List<String> list=Collections.synchronizedList(new ArrayList<>());
        //3.List<String> list=new CopyOnWriteArrayList<>();
        //CopyOnwrite写入时复制 COW 计算机程序设计领域的一种优化策略
        //多个线程调用的时候,list读取的时候,固定的,写入(覆盖)
        //在写入的时候避免覆盖,造成数据问题
        //读写分离
        //CopyOnWriteArrayList比Vector Nb在哪里
        
        List<String> list=new CopyOnWriteArrayList<>();
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                list.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(list);
            },String.valueOf(i)).start();
        }
    }
}

```

> Set不安全

```java
package unsafe;

import java.util.Collections;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;
import java.util.concurrent.CopyOnWriteArraySet;
import java.util.concurrent.locks.Condition;

public class SetTest {
    public static void main(String[] args) {
//        Set<String> set = new HashSet<>();
        //hashset
     //解决方法1   Set<s> set = Collections.synchronizedSet(new HashSet<>());
        //解决方法2
        Set<String> set = new CopyOnWriteArraySet<>();
        for (int i = 0; i < 30; i++) {
            new Thread(()->{
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(set);
            },String.valueOf(i)).start();
        }
    }
}

```

hashSet底层是什么???

```java
public HashSet() {
        map = new HashMap<>();
    }
//add set 本质就是 map key是无法重复的
public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
//不会变的常量
private static final Object PRESENT = new Object();
```

> Map不安全

```java
package unsafe;

import java.util.Collections;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;
import java.util.concurrent.CopyOnWriteArraySet;
import java.util.concurrent.locks.Condition;

public class SetTest {
    public static void main(String[] args) {
//        Set<String> set = new HashSet<>();

        //hashset
     //解决方法1   Set<s> set = Collections.synchronizedSet(new HashSet<>());
        //解决方法2
        Set<String> set = new CopyOnWriteArraySet<>();
        for (int i = 0; i < 30; i++) {
            new Thread(()->{
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(set);
            },String.valueOf(i)).start();
        }
    }
}

```

##### 7、Callable

```java
package Callalble;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        MyThread myThread = new MyThread();
        FutureTask futureTask = new FutureTask<>(myThread);
        new Thread(futureTask,"A").start();
        String s = (String) futureTask.get();//这个get方法可能会产生阻塞
        //或者使用异步通信吃力
        System.out.println(s);
    }

}
class MyThread implements Callable<String>{

    @Override
    public String call() throws Exception {
        System.out.println("call()");
        return "123";
    }
}

```

细节：

1. 有缓存
2. 结果可能需要等待，会阻塞

##### 8、常用的辅助类

---

###### 8.1 CountDownLatch

```java
package add;

import java.util.concurrent.CountDownLatch;

public class CountDpownLathTest {
    public static void main(String[] args) throws InterruptedException {
        //总数是6
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 0; i < 6; i++) {
            new  Thread(()->{
                System.out.println(Thread.currentThread().getName()+"Go out");
                countDownLatch.countDown();
            },String.valueOf(i)).start();
        }
        countDownLatch.await();//等待计数器归零
        System.out.println("close Door");
    }
}

```

原理：

`countDownLatch.countDown();`//数量-1

`countDownLatch.await();`//等待计数器归零，然后再往下执行

每次有线程调用`countDown（）`数量-1，假设计数器变为0，`countDownLatch.await()`就会被唤醒，继续执行。

###### 8.2、 CycliBarrier

```java
package add;

import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class CyclicBarrierDemo {
    public static void main(String[] args) {
        /**
         * 集齐七颗龙珠召唤神龙
         * 召唤龙珠的线程
         */
        CyclicBarrier c = new CyclicBarrier(7, () -> {
            System.out.println("召唤升龙");
        });
        for (int i = 0; i < 7; i++) {
            final int temp=i;
            //lanbada能操作到i吗
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"收集"+temp+"个龙珠");
                try {
                    c.await();//等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}

```



###### 8.3 、Semaphore

Semaphore:信号量

限流使用

```java
package add;

import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

public class SemaphoreDemo{
    public static void main(String[] args) {
        //线程数量：停车位
        Semaphore semaphore = new Semaphore(3);
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"抢到车位");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName()+"离开车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release();//release 释放
                }
            }).start();
        }
    }
}

```

原理：

`semaphore.acquire();`获得，假设如果已经满了，等待，等待被释放为止

`semaphore.release()`释放，会将当前的信号量释放+1，然后唤醒等待的线程！！！

作用：多个共享资源互斥的使用！并发限流，控制最大线程数！！！

##### 9、读写锁

![image-20211101093322703](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211101093322703.png)

```java
package rw;

import sun.security.krb5.RealmException;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

//Read Write Lock

/**
 * 独占锁（写锁） 一次只能被一个线程占有
 * 共享锁（读锁） 多个线程可以同时占有
 * 读-读 可以共存
 * 读写  不可共存
 * 写写  不可共存
 */
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        MyCacheLock myCacheLock = new MyCacheLock();
        //写入操作
        for (int i = 0; i < 5; i++) {
            final int temp=i;
            new Thread(()->{myCacheLock.put(temp+"",temp+"");},String.valueOf(i)).start();
        }
        for (int i = 0; i < 5; i++) {
            final int temp=i;
            new Thread(()->{
                myCacheLock.get(temp+"");
            },String.valueOf(i)).start();
        }
    }
}
/**
 * 自定义缓存
 */
class MyCache{
    private volatile Map<String,Object> map=new HashMap<>();
    //存，写
    public void put(String key,Object value){
        System.out.println(Thread.currentThread().getName()+"写入"+key);
        map.put(key,value);
        System.out.println(Thread.currentThread().getName()+"写入ok");
    }
    //取，读
    public void get(String key){
        System.out.println(Thread.currentThread().getName()+"读取"+key);
        Object o=map.get(key);
        System.out.println(Thread.currentThread().getName()+"读取ok");
    }
}
//加锁
class MyCacheLock{
    private volatile Map<String,Object> map=new HashMap<>();
    //读写锁
    private ReadWriteLock readWriteLock=new ReentrantReadWriteLock();
    //存，写
    public void put(String key,Object value){
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"写入"+key);
            map.put(key,value);
            System.out.println(Thread.currentThread().getName()+"写入ok");
        } catch (Exception e) {
            e.printStackTrace();
        }
        finally {
            readWriteLock.writeLock().unlock();
        }

    }
    //取，读
    public void get(String key){
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"读取"+key);
            Object o=map.get(key);
            System.out.println(Thread.currentThread().getName()+"读取ok");
        } catch (Exception e) {

        }finally {
            readWriteLock.readLock().unlock();
        }

    }
}
```

##### 10、阻塞队列

![image-20211101103844989](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211101103844989.png)

阻塞队列

![image-20211101104105009](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211101104105009.png)

**BlockingQueue**

![image-20211101105606229](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211101105606229.png)

![image-20211101105741667](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211101105741667.png)

什么情况下我们会使用阻塞队列，多线程并发处理，线性池

**学会使用队列**

添加，移除

**四组API**

| 方式       | 抛出异常 | 有返回值 | 阻塞等待 | 超时等待 |
| ---------- | -------- | -------- | -------- | -------- |
| 添加       | add      | offer()  | put()    | offer()  |
| 移除       | remove   | poll()   | take()   | poll()   |
| 判断队列首 | element  | peek     | ...      | ..       |

1、抛出异常

```java
//抛出异常
    public void test1(){
        //队列的大小
        ArrayBlockingQueue arrayBlockingQueue = new ArrayBlockingQueue<>(3);
        System.out.println(arrayBlockingQueue.add("a"));
        System.out.println(arrayBlockingQueue.add("b"));
        System.out.println(arrayBlockingQueue.add("c"));
//        Exception in thread "main" java.lang.IllegalStateException: Queue full 抛出异常
//        System.out.println(arrayBlockingQueue.add("d"));
        System.out.printf("", arrayBlockingQueue.remove());
        System.out.printf("", arrayBlockingQueue.remove());
        System.out.printf("", arrayBlockingQueue.remove());
//        Exception in thread "main" java.util.NoSuchElementException
//        System.out.printf("", arrayBlockingQueue.remove());
    }
```



2、不会抛出异常

```java
//有返回值，无异常
    public void test2(){
        ArrayBlockingQueue arrayBlockingQueue = new ArrayBlockingQueue<>(3);
        System.out.printf("", arrayBlockingQueue.offer("a"));
        System.out.printf("", arrayBlockingQueue.offer("b"));
        System.out.printf("", arrayBlockingQueue.offer("c"));
        System.out.printf("", arrayBlockingQueue.offer("c"));
        System.out.printf("", arrayBlockingQueue.offer("c"));
        System.out.println(arrayBlockingQueue.poll());
        System.out.println(arrayBlockingQueue.poll());
        System.out.println(arrayBlockingQueue.poll());
        System.out.println(arrayBlockingQueue.poll());

    }
```



3、阻塞等待

```java
   //等待，阻塞(一直阻塞)
    public void test3() throws InterruptedException {
        ArrayBlockingQueue arrayBlockingQueue = new ArrayBlockingQueue<>(3);
        //一直阻塞
        arrayBlockingQueue.put("a");
        arrayBlockingQueue.put("b");
        arrayBlockingQueue.put("c");
//        arrayBlockingQueue.put("d"); 队列无位置，一直阻塞
//        arrayBlockingQueue.put("e");
        System.out.println(arrayBlockingQueue.take());
        System.out.println(arrayBlockingQueue.take());
        System.out.println(arrayBlockingQueue.take());
//        System.out.println(arrayBlockingQueue.take());//没有该元素，会阻塞等待


    }
```



4、超时等待

```java
//等待，阻塞（等待，超时）
    public void test4() throws InterruptedException {
        ArrayBlockingQueue arrayBlockingQueue = new ArrayBlockingQueue<>(3);
        arrayBlockingQueue.offer("a");
        arrayBlockingQueue.offer("ab");
        arrayBlockingQueue.offer("ac");
        arrayBlockingQueue.offer("ad",2, TimeUnit.SECONDS);
        arrayBlockingQueue.poll();
        arrayBlockingQueue.poll();
        arrayBlockingQueue.poll();
        arrayBlockingQueue.poll(2,TimeUnit.SECONDS);
    }
```

> SynchronousQueue 同步队列

没有容量

进去一个元素，必须等待取出来之后，才能再往里面放一个元素

```java
package bq;

import java.util.concurrent.BlockingQueue;
import java.util.concurrent.SynchronousQueue;
import java.util.concurrent.TimeUnit;

/**
 * 同步队列
 * 和其他的BlockingQueue 不一样，
 */
public class SynchronousQueneDemo {
    public static void main(String[] args) {
        BlockingQueue<String> blockingQueue = new SynchronousQueue<>();//同步队列
        new Thread(() -> {
            try {
                System.out.println(Thread.currentThread().getName() + "put 1");
                blockingQueue.put("1");
                System.out.println(Thread.currentThread().getName() + "put 2");
                blockingQueue.put("2");
                System.out.println(Thread.currentThread().getName() + "put 3");
                blockingQueue.put("3");
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "T1").start();
        new Thread(() -> {
            try {
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName()+blockingQueue.take());
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName()+blockingQueue.take());
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName()+blockingQueue.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "T2").start();
    }
}

```

##### **11、线程池（重点）**

> 池化技术

程序的运行，本质：占用系统的资源！优化资源的使用！=》池化技术

线程池，连接池，内存池，对象池........

池化技术：事先准备好一些资源，有人要用，就来我这里拿，用完之后还给我。

默认大小：2

**线程池的好处：**

1、降低资源的消耗

2、提高响应的速度

3、方便管理

`线程复用，可以控制最大并发量、管理线程`

```java
package pool;

import java.util.concurrent.Executor;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

//Executors 工具类 3大方法
//使用线程池之后，使用线程池来创建线程
public class Demo {
    public static void main(String[] args) {
//        ExecutorService threadPool = Executors.newSingleThreadExecutor();//单个线程
//        ExecutorService threadPool = Executors.newFixedThreadPool(5);//创建一个固定的线程池的大小
        ExecutorService threadPool = Executors.newCachedThreadPool();
        try {

            for (int i = 0; i < 10; i++) {
                threadPool.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + "ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }
}

```

> 七大参数

源码分析

```java
public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
 public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```

