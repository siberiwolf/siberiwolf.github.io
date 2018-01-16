---
layout: post
title:  "使用Redis实现MySQL缓存层"
date:   2018-01-15 22:00:00
categories: cache
---

| id | name   | credit | year | 
|----|--------|--------|------| 
| 1  | tom    | 88     | 1980 | 
| 2  | jerry  | 78     | 1980 | 
| 3  | robert | 99     | 1981 | 
| 4  | paul   | 69     | 1982 | 
| 5  | john   | 88     | 1982 | 
一张学生表，id为主键，表名t_student 

Q:如何在Redis里保存每一条学生记录 
A:第一种方式，每一条记录保存一个key/value，比如key为t_student_1，value为一个json串(使用protobuf序列化为byte[]空间效率会提升很多吧) 
  第二种方式，保存为一个hashtable，key为t_student，field为1，value同上，hashtable无法针对某一个内部的key做expire操作，只能expire整个hashtable
  
Q:只根据id做查询，select id, name, credit, year from t_student where id = 1 
A:使用GET key命令或者HGET key field命令获取相应记录，无需额外存储任何信息 

Q:需要根据非主键列查询，select id, name, credit, year from t_student where credit = 88 
A:需要增加一个sorted set作为索引，如果没有索引，需要做全表扫描，时间复杂度O(N)，索引key为t_student_index_credit，通过ZADD key score(使用credit的数值) id, 
  上表的该索引[4(69),2(78),1(88),5(88),3(99)]，索引是有序的，指定credit值查找时可以进行二分查找，时间复杂度为O(log(N))，查询时通过ZRANGEBYSCORE key min max 
  获取到指定credit对应的id集合，再遍历id集合获取到记录，上面的查询对应命令ZRANGEBYSCORE t_student_index_credit 88 88，会返回[1,5] 

Q:多列查询，select id, name, credit, year from t_student where credit = 88 and year = 1980 
A:第一种方式，再增加一个索引t_student_index_year，[1(1980),2(1980),3(1981),4(1982),5(1982)]，根据credit=88查询到集合[1,5]，根据year=1980查询到集合[1,2]，再计算两个集合的交集 
  第二种方式，保存一个嵌套的sorted set，先根据credit排序，再根据year排序，类似于[[4](69),[2](78),[1,5](88),[3](99)]，还没实现？？？MySQL的复合索引左优先原则即可体现，根据这个索引结构， 
  查询credit = 88可以使用该索引，查询credit = 88 and year = 1980可以使用该索引，查询year = 1980或者year = 1980 and credit = 88则无法使用该索引