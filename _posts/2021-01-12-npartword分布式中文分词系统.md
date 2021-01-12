---
layout:     post
title:      npartword分布式中文分词系统
subtitle:   中文分词
date:       2021-01-12
author:     hughnian
header-img: https://picsum.photos/1024/768
catalog: true
tags:
    - Golang
    - PHP
    - 微服务
    - 分布式
    - 中文分词
---

## 缘起

提到分词一直都是一个比较底层的技术，说简单点就是一个中文分词系统，说复杂的话它也是属于人工智能范畴，确切说是属于NLP自然语言处理范畴。
npw是一个用Golang开发的分词系统，里面也用到了NLP的隐马尔可夫模型，维特比算法等，并没有对词汇进行学习处理。我们只是NLP的应用，真正的人工智能NLP
需要模型训练，需要不停的对不同模型的加解码，得出最优解，这就很深了，npw不属于这个范畴(￣▽￣)"。


<div align="center">
    <a href="http://www.niansong.top"><img src="https://raw.githubusercontent.com/HughNian/npartword/master/logo/npartword_logo1.png" alt="npw logo" width="230"></a>
</div>  

## npw项目地址
[https://github.com/HughNian/npartword](https://github.com/HughNian/npartword){:target="_blank"}

## npw介绍
npw：npartword，golang实现的分布式中文分词系统，主体分词逻辑有两个部分。   

- 1.前缀树查找字典，通过disctance或mmseg算法过滤分词    

- 2.维特比算法解隐马尔可夫模型，对词进行隐状态标注分词   

分布式分词服务系统的实现，通过nmid的worker方式实现。(对于nmid的微服务系统可以看这里的介绍 [我是连接](https://www.niansong.top/2020/06/16/nmid%E5%88%86%E5%B8%83%E5%BC%8F%E5%BE%AE%E6%9C%8D%E5%8A%A1%E8%B0%83%E5%BA%A6%E7%B3%BB%E7%BB%9F/){:target="_blank"} )       

- 1.分词系统服务端，需要实现nmid的worker，服务的实现十分简单，无需考虑通信问题，这些nmid解决。   

- 2.分词系统服务调用，只要通过nmid的client调用即可，任何nmid的client都可以随时跨服务器的使用分词系统，目前可以有c客户端，golang客户端，php客户端。

```php
php调用示例

//普通分词
fname - PartWordsM1
array(3) {
  [0]=>
  int(0)
  [1]=>
  string(2) "ok"
  [2]=>
  string(77) "南京大学|城|书店|，|长春市|长春|药店|，|研究|生命|起源|"
}

//mmseg分词
fname - PartWordsM2
array(3) {
  [0]=>
  int(0)
  [1]=>
  string(2) "ok"
  [2]=>
  string(77) "南京|大学城|书店|，|长春市|长春|药店|，|研究|生命|起源|"
}

//隐马尔可夫模型
fname - PartWordsM3
array(3) {
  [0]=>
  int(0)
  [1]=>
  string(2) "ok"
  [2]=>
  string(75) "南京大学|城书店|，|长春市|长春药店|，|研究|生命|起源|"
}

```
