---
layout: post
title:  "Java同步机制"
date:   2018-08-25 12:00:00
categories: thread
---

<a href="https://wiki.openjdk.java.net/display/HotSpot/Synchronization">open jdk Synchronization</a>

Java的对象头包含mark word和指向类实例的指针，如果为数组，还包含数组长度。

1.对象创建的时候，判断是否启用了偏向锁(如何启用、关闭偏向锁)

2.如果启用了偏向锁，对象mark word的初始状态为0(thread id)、epoch(???)、age(分代年龄)、1(启用了偏向锁)、01(对象未锁定)

3.如果未启用偏向锁，对象mark word的初始状态为hash code、age(分代年龄)、0(未启用偏向锁)、01(对象未锁定)

4.未启用偏向锁的情况下，如果有线程尝试锁定对象，会在锁定线程的当前栈帧中初始化一个lock record，存储了对象的mark word ，然后通过cas操作把对象的mark word修改为指向lock record的指针，
锁状态修改为00(对象已锁定)，这个称为轻量锁；如果cas操作不成功，先判断mark word中的lock record pointer是否指向当前线程的栈帧，如果是，表示是当前线程锁定了该对象，可继续执行；
否则说明对象已经被其他线程锁定，膨胀为重量锁

5.重量级级锁的情况下，mark word中存储指向对象管程(monitor)的指针，重量级锁的底层实现是调用系统的mutex lock操作，会导致从用户态到内核态的切换，开销比较大。
         
                
                
                
                
                