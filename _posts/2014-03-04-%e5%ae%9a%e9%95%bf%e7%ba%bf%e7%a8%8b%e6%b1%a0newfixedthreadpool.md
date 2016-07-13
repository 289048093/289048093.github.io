---
id: 275
title: 定长线程池newFixedThreadPool
date: 2014-03-04T10:42:51+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=275
permalink: /archives/275
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - java
  - 多线程
tags:
  - Executors
  - java
  - 线程池
---
在多线程“生产者-消费者”模式中，为了避免线程陷入竞争而耗尽资源，通常可以通过Executors.newFixedThreadPool(num)限制线程的数量来解决问题，但是如果“生产者”数量持续大于“消费者”数量问题就容易产生了：

<pre class="brush: java; title: ; notranslate" title="">public static void main(String[] args) throws InterruptedException {
        int num = 10000000;
        ExecutorService es = Executors.newFixedThreadPool(2);
        for (int i = 0; i &lt; num; i++) {
            es.execute(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName());
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();  //To change body of catch statement use File | Settings | File Templates.
                    }
                }
            });
        }
        es.shutdown();
        System.out.println("main end...");
    }
</pre>

这里创建了一千万个线程，限制线程池大小为2；为了让&#8221;消费者&#8221;消费速度小于生产速度更明显，让创建的线程休息1秒。本人运行环境机器是4G内存，jvm堆空间默认大小，运行后抛出OutOfMemoryError，这是因为线程创建线程后，但是由于消费速度小于生产速度，线程池满后线程虽然没有立即执行，而是放在了等待队列中，当持续消费速度小于生产速度时，等待队列则持续增长，最终把堆空间撑爆了。

解决这个问题可以配合信号变量使用，使线程池满后，生产者暂停生产，陷入阻塞状态：

<pre class="brush: java; title: ; notranslate" title="">public static void main(String[] args) throws InterruptedException {
        int num = 10000000;
        int threadNum = 2;
        ExecutorService es = Executors.newFixedThreadPool(threadNum);
        final Semaphore semaphore = new Semaphore(threadNum);    //创建与线程池大小相当的信号变量
        for (int i = 0; i &lt; num; i++) {
            semaphore.acquire(); //线程池满后主线程将暂时阻塞
            es.execute(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName());
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();  //To change body of catch statement use File | Settings | File Templates.
                    }finally{
                        semaphore.release(); //线程运行完后信号释放，主线程恢复运行
                    }
                }
            });
        }
        es.shutdown();
        System.out.println("main end...");
    }
</pre>

如此，堆空间将不再担心资源枯竭,只是主线程会受到阻塞，只需另起一个线程代替主线程即可。