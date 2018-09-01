---
layout: post
title:  "Java ReentrantLock"
date:   2018-08-30 20:00:00
categories: thread
---

Java ReentrantLock实现解析

1.ReentrantLock的lock、unlock操作都是通过field对象sync来实现，内部类Sync继承于AbstractQueuedSynchronizer，其余两个内部类NonfairSync和FairSync继承Sync

2.NonfairSync和FairSync的区别在于FairSync保证先调用lock操作的线程，就会先获得锁，NonfairSync不保证这个优先规则，先通过CAS操作把自己加入等待队列的线程，先获得锁

3.lock过程解析
sync对象从父类AbstractQueuedSynchronizer继承一个volatile的int型状态值state，在lock未被线程持有的情况下，state值为0

第一步<br/>
lock通过一个CAS操作尝试把state从期望值0修改为1，如果修改成功，代表线程持有了该锁，修改持有锁的线程为当前线程，lock过程结束(轻量级锁是通过一个CAS操作修改对象
头中的mark word，与此操作有些类似)

第二步<br/>
如果第一步的CAS操作失败，代表该锁已经被其他线程持有，在acquire方法中调用tryAcquire方法和acquireQueued方法。tryAcquire方法中再次判断state是否为0，如果为0，再尝试
一次CAS修改state的操作(此处是自旋一次)，修改成功则获取锁，如果还不成功，判断当前线程是否为持有锁的线程，如果是，state自增(可重入锁的概念)，成功获取锁；如果还未成功
获取锁，通过addWaiter方法把线程加入等待链表，如果链表还是空的，通过CAS操作设置一个不带线程信息的node为头结点，把当前线程的node链接到头结点之后
(New Node(无线程信息)->node(当前线程))，否则，把当前线程结点链接到尾结点之后(New Node(无线程信息)->node(前面的等待线程)->node(当前线程))；
在acquireQueued方法中，判断如果当前node的前置node为无线程信息的头结点，再尝试一次CAS操作获取锁，失败之后，通过Unsafe的park方法block当前线程；如果block过程被线程
中断唤醒，通过Thread的interrupt方法中断当前线程。

在unlock方法中，如果status为0了，则设置当前持有锁的线程为null，在AbstractQueuedSynchronizer的unparkSuccessor方法中通过Unsafe的unpark方法唤醒头结点之后那个结点上
的等待线程；被唤醒的线程通过CAS操作获取锁之后，把自己持有的节点node设为头结点，并把节点上的线程信息设置为null



         
                
                
                
                
                