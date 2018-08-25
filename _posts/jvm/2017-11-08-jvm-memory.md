---
layout: post
title:  "JVM运行时数据区域"
date:   2017-11-08 22:00:00
categories: jvm
---

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;}
.tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;}
.tg .tg-baqh{text-align:center;vertical-align:top}
</style>
<table class="tg">
  <tr>
    <th class="tg-baqh">方法区（线程共享）</th>
    <th class="tg-baqh">stack</th>
    <th class="tg-baqh">native stack</th>
  </tr>
  <tr>
    <td class="tg-baqh">heap（线程共享）</td>
    <td class="tg-baqh" colspan="2">程序计数器</td>
  </tr>
</table>

程序计数器(program counter register)：每个线程都拥有自己的pc register，存储线程执行的当前方法的当前虚拟机指令的地址(非native方法，如果是native方法，pc register undefined)

栈(stack)：每个线程创建时创建自己的栈，栈中包含一个个帧(frame)，线程消亡的时候栈就跟着消亡；如果是定长的栈，在帧过多的时候会抛出StackOverflow异常；
如果是动态扩展的栈，在无法分配栈空间的时候抛出OutOfMemory异常

帧(frame)：每个方法开始执行的时候创建一个帧，帧中包含本地变量数组(local variables)和指令栈(operand stack)以及对运行时常量池的引用

堆(heap)：对象实例和数组对象存储在堆中

方法区(method area)：类的元数据信息存储在方法区(运行时常量池、field、method data)

运行时常量池(run-time constant pool)：每个类或者接口有自己的运行时常量，存储于运行时常量池

本地方法栈(native method stack)：执行native方法时创建的栈