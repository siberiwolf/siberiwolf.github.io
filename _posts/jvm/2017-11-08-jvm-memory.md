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