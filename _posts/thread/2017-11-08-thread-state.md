---
layout: post
title:  "线程状态转换"
date:   2017-11-08 22:00:00
categories: thread
---

                    ____________________________________________________________________
                    |RUNNABLE                                                           |
        t.start()   |                           Thread.yield()                          |
	NEW------------>|                   READY<--------------------RUNNING               |------------------->TERMINATED
                    |                                                                   |                       |
	|---------------|___________________________________________________________________|                       |
	|                                                                     |           |                         |
	|                                                                     |           |                         |
	|                                           ____________________  time elapsed    |                         |
	|------Thread.sleep(long milliseconds)----->|                   |     |           |                         |
	|--------o.wait(long milliseconds)--------->|                   |     |           |                         |
    |--------t.join(long milliseconds)--------->|   TIMED_WAITING   |---->|           |                         |
    |--LockSupport.parkNanos(long nanoseconds)->|                   |------------------------------------------>|
    |-LockSupport.parkUntil(long milliseconds)->|___________________|                 |                         |
    |                                               |           |                     |                         |
    |                                               |           |                     |                         |
    |                                           o.notify()  o.notifyAll()             |                         |
    |                       ________________        |           |                     |                         |
    |------o.wait()-------->|               |       |           |                     |                         |
    |------t.join()-------->|   WAITING     |------------------------------------------------------------------>|   
    |-LockSupport.park()--->|_______________|       |           |                     |   
    |                           |       |           |           |                     |
    |                           |       |           |           |                     |
    |                   o.nitify()  o.notifyAll()   |           |                     |
    |                           |       |           |           |                     |
    |       synchronized        |       |           |           |                     |
    |----------------------------------------------------------------->BLOCKED------->|
                    
                
                
                