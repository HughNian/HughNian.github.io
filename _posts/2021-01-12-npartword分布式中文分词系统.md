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

提到分词一直都是一个比较底层的技术，说简单点就是一个分词系统，说复杂的话它也是属于人工智能范畴，确切说是属于NLP自然语言处理范畴。
npw是一个用Golang开发的中文分词系统，里面也用到了NLP的隐马尔可夫模型，维特比算法等，并没有对词汇进行学习处理。我们只是NLP的应用，真正的人工智能NLP
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

- 3.加入情感词典，文本情感分类算法，对文本进行情感评分，如积极，消极，否定等，一般分值越高文本的积极性越高   

分布式分词服务系统的实现，通过nmid的worker方式实现。(对于nmid的微服务系统可以看这里的介绍 [我是连接](https://www.niansong.top/2020/06/16/nmid%E5%88%86%E5%B8%83%E5%BC%8F%E5%BE%AE%E6%9C%8D%E5%8A%A1%E8%B0%83%E5%BA%A6%E7%B3%BB%E7%BB%9F/){:target="_blank"} )       

- 1.分词系统服务端，需要实现nmid的worker，服务的实现十分简单，无需考虑通信问题，这些nmid解决。   

- 2.分词系统服务调用，只要通过nmid的client调用即可，任何nmid的client都可以随时跨服务器的使用分词系统，目前可以有c客户端，golang客户端，php客户端。

```php
php调用示例

$host = "xxx.xxx.x.xx";
$port = xxx;
$client = new ClientExt($host, $port);
$client->connect();

//普通分词
//fname - PartWordsM1
$text = "南京大学城书店，长春市长春药店，研究生命起源";
$params = msgpack_pack(array($text, "1"));
$client->dowork("PartWordsM1", $params, function($ret){
        var_dump($ret);
});

array(3) {
  [0]=>
  int(0)
  [1]=>
  string(2) "ok"
  [2]=>
  string(77) "南京大学|城|书店|，|长春市|长春|药店|，|研究|生命|起源|"
}

//mmseg分词
//fname - PartWordsM2
$params = msgpack_pack(array($text, "1"));
$client->dowork("PartWordsM2", $params, function($ret){
        var_dump($ret);
});

array(3) {
  [0]=>
  int(0)
  [1]=>
  string(2) "ok"
  [2]=>
  string(77) "南京|大学城|书店|，|长春市|长春|药店|，|研究|生命|起源|"
}

//隐马尔可夫模型
//fname - PartWordsM3
$params = msgpack_pack(array($text, "1"));
$client->dowork("PartWordsM3", $params, function($ret) {
        var_dump($ret);
});

array(3) {
  [0]=>
  int(0)
  [1]=>
  string(2) "ok"
  [2]=>
  string(75) "南京大学|城书店|，|长春市|长春药店|，|研究|生命|起源|"
}

//获取文本情感值
$text2 = "世事无常，你是人间琳琅，众人平庸，你是人间星光，万事浮沉，你是人间归途。";
$params = msgpack_pack(array($text, "3"));
$client->dowork("PartWordsM1", $params, function($ret){
        var_dump($ret);
});

array(3) {
  [0]=>
  int(0)
  [1]=>
  string(2) "ok"
  [2]=>
  string(213) "世事/x| 无常/ne| ，/x| 你/x| 是/po| 人间/x| 琳琅/x| ，/x| 众人/x| 平庸/x| ，/x| 你/x| 是/po| 人间/x| 星光/x| ，/x| 万事/x| 浮沉/x| ，/x| 你/x| 是/po| 人间/x| 归途/x| 。/x| =2.00"
  //获取情感值分词会带上情感词性，最后会有情感值分数
}

$text2 = "今天我想你了 但是这不影响我打游戏 不影响我睡觉 只是悄咪咪抹了抹眼泪 感叹了一下最后还是没能牵到你的手";
$params = msgpack_pack(array($text, "3"));
$client->dowork("PartWordsM1", $params, function($ret){
        var_dump($ret);
});

array(3) {
  [0]=>
  int(0)
  [1]=>
  string(2) "ok"
  [2]=>
  string(319) "今天/x| 我/x| 想/po| 你/x| 了/x|  /x| 但是/x| 这/x| 不/ga| 影响/x| 我/x| 打游戏/x|  /x| 不/ga| 影响/x| 我/x| 睡觉/x|  /x| 只是/x| 悄/po| 咪咪/x| 抹/x| 了/x| 抹/x| 眼泪/x|  /x| 感叹/x| 了/x| 一下/x| 最后/x| 还是/x| 没/ga| 能/po| 牵/x| 到/po| 你/x| 的/x| 手/x| 。/x| =0.50"
}
//同样是表达爱意，显然这句的情感不如上一句，所以他的分值低一些
```

## 最后&展望
目前从实际效果看，普通分词和基于mmseg算法的分词效果更好些，这需要归功于搜狗的分词字典库，字典库是已经训练好的分词结果，再加上mmseg等算法，
所以结果也会更好些，而隐马尔可夫模型由于训练的结果集有些年头以及词汇更新的频繁，所以目前的这个隐马尔可夫模型分词的准确率不高，要想提高的话，
只能对样本不断进行分词训练了，使用更好的结果集才能提升，这就属于NPL的范畴了。  
最近增加了情感词典和文本的情感分类算法，目前情感词典和算法可以满足大部分场景，当然如果是特定的场景下还需要对情感词模型进行训练，得到更符合特定场景的情感词典。