####  1：什么是JUC

🤑

---

 JUC就是java.util.concurrent下面的类包，专门用于多线程的开发。

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
本质：
    public ThreadPoolExecutor(int corePoolSize,//核心线程池大小
                              int maximumPoolSize,//最大核心线程池大小
                              long keepAliveTime,//超时了没有人调用就会释放
                              TimeUnit unit,//超时单位
                              BlockingQueue<Runnable> workQueue,//阻塞队列
                              ThreadFactory threadFactory,//线程工厂，创建线程的，一般不用动
                              RejectedExecutionHandler handler//拒绝策略) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.acc = System.getSecurityManager() == null ?
                null :
                AccessController.getContext();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

![image-20211101152946132](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211101152946132.png)

![image-20211101152955231](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211101152955231.png)

> 手动创建线程池

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

> 四种拒绝策略

```java 
package pool;

import java.util.concurrent.*;
//new ThreadPoolExecutor.AbortPolicy()//人满了，还有人进来，不处理这个人的，抛出异常
//new ThreadPoolExecutor.CallerRunsPolicy());//哪来的去哪里
//new ThreadPoolExecutor.DiscardPolicy());//队列满了，丢掉任务，不会抛出异常
//new ThreadPoolExecutor.DiscardOldestPolicy());//队列满了，尝试去和最早的竞争，也不会抛出异常
public class Demo2 {
    public static void main(String[] args) {
        ExecutorService threadPool = new ThreadPoolExecutor(2,
                5,
                3,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.DiscardOldestPolicy());//队列满了，尝试去和最早的竞争，也不会抛出异常

        try {
            //
            for (int i = 0; i < 9; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+"----OK");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            threadPool.shutdown();
        }
    }
}

```

```java
//new ThreadPoolExecutor.AbortPolicy()//人满了，还有人进来，不处理这个人的，抛出异常
//new ThreadPoolExecutor.CallerRunsPolicy());//哪来的去哪里
//new ThreadPoolExecutor.DiscardPolicy());//队列满了，丢掉任务，不会抛出异常
//new ThreadPoolExecutor.DiscardOldestPolicy());//队列满了，尝试去和最早的竞争，也不会抛出异常
```

> 小结和扩展

```java
/**
 * 最大线程到底该如何定义
 * 1、cpu密集型 几核，就是几，可以保持cpu的效率最高
 * 2、io 密集型 判断你程序中十分耗IO的线程
 * 程序 15个大型任务，IO十分占用资源
 */
```

##### **12、函数式接口（必须掌握）**

---

新时代的程序员：lambda表达式，链式编程，函数式接口，Stream流式接口

> 函数式接口：只有一个方法

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

![image-20211102093953556](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211102093953556.png)

**代码测试：**

```java
package function;

import java.util.function.Function;

/**
 * Function 函数型接口，有一个输入参数，有一个输出
 * 只要是函数型接口可以用lambda表达式简化
 */
public class Demo {
    public static void main(String[] args) {
//        Function function = new Function<String,String>() {
//            @Override
//            public String apply(String o) {
//                return null;
//            }
//        };
        Function function=(str)->{return str;};
        System.out.println(function.apply("ads"));
    }
}

```

```java

package function;

import java.util.function.Predicate;

public class Demo2 {
    public static void main(String[] args) {
        //判断字符串是否为空
        Predicate<String> predicate = new Predicate<String>() {
            @Override
            public boolean test(String s) {
                return s.isEmpty();
            }
        };
        Predicate<String> predicate1=(str)->{return str.isEmpty();};
        System.out.println(predicate1.test(""));
    }
}

```

> Consumer 消费型接口

```
package function;

import java.util.function.Consumer;

public class Demo3 {
    public static void main(String[] args) {
        /**
         * Consumer 消费型接口： 只有输入，没有返回值
         *
         */
        Consumer<String> consumer = new Consumer<String>() {
            @Override
            public void accept(String o) {
                System.out.println(o);
            }
        };
        Consumer<String> consumer1=(str)->{System.out.println(str);};
        consumer.accept("adfsf");
        consumer1.accept("sfdsfsd");
    }
}
```

> Supplier 供给型接口

```java
package function;

import java.util.function.Supplier;

public class Demo4 {
    /**
     * Supplier 没有参数，只有返回值
     */
    public static void main(String[] args) {
//        Supplier<Integer> supplier = new Supplier<Integer>() {
//
//            @Override
//            public Integer get() {
//                System.out.println("get()");
//                return 1024;
//            }
//        };
        Supplier supplier1=()->{ return 1024; };
        System.out.println(supplier1.get());
    }
}

```

##### 13、Stream流式计算

> 什么是Stream流式计算

```java
package Stream;

import java.util.Arrays;
import java.util.List;

public class Test {
    public static void main(String[] args) {
        User u1 = new User(1, "a", 21);
        User u2 = new User(2, "b", 22);
        User u3 = new User(3, "c", 23);
        User u4 = new User(4, "d", 24);
        User u5 = new User(6, "e", 25);
        //集合就是存储
        List<User> users = Arrays.asList(u1, u2, u3, u4, u5);
        //计算交给Stream流
        //链式编程
        users.stream()
                .filter(u->{return u.getId()%2==0;}).
                filter(u->{return u.getAge()>=23;}).
                map((u)->{return u.getName().toUpperCase();}).

                forEach(System.out::println);
    }
}

```

##### 14、ForkJoin

> 什么是ForkJoin

forkJoin在JDK1.7,并行执行任务，提高效率，大数据量

![image-20211102104857362](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211102104857362.png)

> ForkJoin特点：工作窃取

![image-20211102105111881](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211102105111881.png)

> ForkJoin

```java
package forkjoin;

import java.util.concurrent.RecursiveTask;

/**
 * 求和计算任务 Stream并行流
 */
public class ForkjoinDemo extends RecursiveTask<Long> {
    private Long start;
    private Long end;
    //灵界值
    private Long temp = 10000L;

    public ForkjoinDemo(Long start, Long end) {
        this.start = start;
        this.end = end;
    }

    //计算方法
    @Override
    protected Long compute() {
        if ((end - start) > temp) {
            Long sum = 0L;
            for (Long i = start; i <= end; i++) {
                sum += i;
            }
            return sum;
        } else {
            //forkjoin
            long millde = (start - end) / 2;//中间值
            ForkjoinDemo task1 = new ForkjoinDemo(start, millde);
            task1.fork();//拆分任务，把任务压入线程队列
            ForkjoinDemo task2 = new ForkjoinDemo(millde + 1, end);
            task2.fork();//拆分任务，把任务压入线程队列
            return task1.join() + task2.join();
        }
    }



}
测试
    package forkjoin;

import java.util.concurrent.ExecutionException;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.ForkJoinTask;
import java.util.stream.LongStream;

public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        test1();
        test2();
    }
    //普通程序员
    public static void test1(){
        Long sum=0L;
        long start = System.currentTimeMillis();
        for (Long i=1L;i<=10_0000_0000;i++){
            sum+=i;
        }
        long end=System.currentTimeMillis();
        System.out.println("普通程序员求和sum="+sum+"时间="+(end-start));
    }
    //会使用ForkJoin
    public static  void test2() throws ExecutionException, InterruptedException {
        long start=System.currentTimeMillis();
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkjoinDemo task = new ForkjoinDemo(0L,10_0000_0000L);
        ForkJoinTask<Long> submit = forkJoinPool.submit(task);//提交任务
        Long sum = submit.get();

        long end= System.currentTimeMillis();
        System.out.println("Forkjoinsum求和="+sum+"时间="+(end-start));
    }
    //使用stream并行流
    public static void test3(){
        long start=System.currentTimeMillis();
        long sum = LongStream.rangeClosed(0L, 10_0000_0000L).parallel().reduce(0, Long::sum);
        long end=System.currentTimeMillis();
        System.out.println("Stream并行流求和sum求和="+sum+"时间="+(end-start));
    }
}

```

##### 15、异步回调

> Future 设计的初衷

```java
package futrue;

import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;

/**
 * 异步调用 CompletableFuture
 * //异步执行
 * 成功回调
 * 失败回调
 */
public class Demo01 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //发起一个请求
        //没有返回值的异步回调
//        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
//            try {
//                TimeUnit.SECONDS.sleep(2);
//            } catch (InterruptedException e) {
//                e.printStackTrace();
//            }
//            System.out.println(Thread.currentThread().getName() + "runAsync=>void");
//        });
//        System.out.println("11111");
//        completableFuture.get();//获得阻塞执行结果
        //有返回值的异步回调
        CompletableFuture<Integer> completableFuture=CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName()+"sdfsaf");
            int i=10/0;
            return 1024;
        });
        completableFuture.whenComplete((t,u)->{
            System.out.println(t);//正常的返回结果
            System.out.println(u);//错误信息
        }).exceptionally((e)->{
            System.out.println(e.getMessage());
            return 233;//可以获取到错误的返回结果
            /**
             * succee Code 200
             * unsuccee 404
             */
        });
    }
}

```

##### 16:JMM

> 请你谈谈你对Volaatile的理解

Volatile是Java虚拟机提供**轻量级的同步机制**

1、保证可见性

<p style="color:red">2、不保证原子性</p>

3、禁止网络重排

> 什么是JMM

JMM ：Java内存模型，不存在的东西，概念！约定！

**关于JMM的一些同步的约定：**

1：线程解锁前，必须把共享变量**立刻**刷回主存

2、线程加锁前，必须读取主存中最新值到工作内存中

3、 加锁和解锁都是一把锁



线程**工作内存**、**主内存**

八种操作：

![image-20211102143712189](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211102143712189.png)

![image-20211102144711895](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211102144711895.png)

**内存交互操作有8种**

- - lock   （锁定）：作用于主内存的变量，把一个变量标识为线程独占状态
  - unlock （解锁）：作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定
  - read  （读取）：作用于主内存变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用
  - load   （载入）：作用于工作内存的变量，它把read操作从主存中变量放入工作内存中
  - use   （使用）：作用于工作内存中的变量，它把工作内存中的变量传输给执行引擎，每当虚拟机遇到一个需要使用到变量的值，就会使用到这个指令
  - assign （赋值）：作用于工作内存中的变量，它把一个从执行引擎中接受到的值放入工作内存的变量副本中
  - store  （存储）：作用于主内存中的变量，它把一个从工作内存中一个变量的值传送到主内存中，以便后续的write使用
  - write 　（写入）：作用于主内存中的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中

　　**JMM对这八种指令的使用，制定了如下规则：**

- - 不允许read和load、store和write操作之一单独出现。即使用了read必须load，使用了store必须write
  - 不允许线程丢弃他最近的assign操作，即工作变量的数据改变了之后，必须告知主存
  - 不允许一个线程将没有assign的数据从工作内存同步回主内存
  - 一个新的变量必须在主内存中诞生，不允许工作内存直接使用一个未被初始化的变量。就是怼变量实施use、store操作之前，必须经过assign和load操作
  - 一个变量同一时间只有一个线程能对其进行lock。多次lock后，必须执行相同次数的unlock才能解锁
  - 如果对一个变量进行lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新load或assign操作初始化变量的值
  - 如果一个变量没有被lock，就不能对其进行unlock操作。也不能unlock一个被其他线程锁住的变量
  - 对一个变量进行unlock操作之前，必须把此变量同步回主内存

问题：程序不知道主内存的值已被修改了

##### 17、Volatile

> 保证程序可见性

```java
package valatile;

import java.util.concurrent.TimeUnit;

public class JMMDemo {
    //不加volatile 程序就会死循环
    //加volatile 可以保证可见性
    private volatile static int num=0;

    public static void main(String[] args) {
        new Thread(()->{
            while (num == 0) {

            }
        }).start();
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        num=1;
        System.out.println(num);
    }
}

```

> 2、不保证原子性

原子性：不可分割

线程A在执行任务的时候，不能被打扰的，也不可能被分割。要么同时成功，要么同时失败。

```java
package valatile;

public class Demo2 {
    //volatile 不保证原子性
    private volatile  static int num=0;
    public static void add(){
        num++;
    }

    public static void main(String[] args) {
        //理论上num结果应该为2万
        for (int i = 0; i < 20; i++) {
            new Thread(()->{
                for (int i1 = 0; i1 < 1000; i1++) {
                        add();
                }
            }).start();
            while (Thread.activeCount()>2){
                Thread.yield();
            }
            System.out.println(Thread.currentThread().getName()+" "+num);
        }
    }
}

```



如果不加Lock和synchronized,怎么保证原子性

![image-20211102160102925](C:\Users\18352\AppData\Roaming\Typora\typora-user-images\image-20211102160102925.png)

使用原子类，解决原子性问题

```java
package valatile;

import java.util.concurrent.atomic.AtomicInteger;

public class Demo2 {
    //volatile 不保证原子性
    private volatile  static  AtomicInteger num=new AtomicInteger();
    public static void add(){
            num.getAndIncrement();
    }

    public static void main(String[] args) {
        //理论上num结果应该为2万
        for (int i = 0; i < 20; i++) {
            new Thread(()->{
                for (int i1 = 0; i1 < 1000; i1++) {
                        add();
                }
            }).start();
            while (Thread.activeCount()>2){
                Thread.yield();
            }
            System.out.println(Thread.currentThread().getName()+" "+num);
        }
    }
}

```

这些类的底层都直接和操作系统挂钩，在内存中修改值，Unsafe类是一个很特殊的存在。

##### 18、彻底玩转单例模式

##### 19、深入理解CAS

##### 20、原子引用

##### 21、各种锁的理解

