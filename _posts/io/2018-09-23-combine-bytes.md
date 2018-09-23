---
layout: post
title:  "如何组合两个byte[]"
date:   2018-09-23 15:00:00
categories: io
---

在网络编程中，一个常见场景是合并两个byte[]，比如合并message header和message body，如何实现这个功能呢？

如果不使用java nio，则必须创建一个大的byte[]，遍历header和body，一个byte一个byte的把数据拷贝到新创建的大byte[]，需要做内存拷贝N(header length)+M(body length)次

如果使用nio中的ByteBuffer，则需要创建一个大的ByteBuffer target，通过target.put(header).put(body)，经过两次System.arraycopy调用完成，内存拷贝两次

如果使用nio的GatheringByteChannel，则无需合并header和body，通过write(ByteBuffer[] srcs, int offset, int length)方法，直接把header和body写入channel，但这个方式只适用于nio channel

netty的解决方案是io.netty.buffer.Unpooled.wrappedBuffer(header, body)，内存0拷贝