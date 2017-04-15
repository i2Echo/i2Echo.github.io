---
title: 洗牌算法问题
date: 2016-09-23 16:03:20
tags: [shuffle, 算法, javascript, 算法优化, 洗牌]
categories: 算法
---

### 问题描述
昨天在宣讲会笔试上碰到一道洗牌算法的笔试题，题目大概就是**有一副54张牌的有序扑克，写一个洗牌算法**；由于时间匆忙，没有过多的思考直接用最简单的方法写了，写到一半发现还有很多可以优化的地方，但是由于是笔试，不好修改就算了，打算回去再把这道题优化一下，顺便看下还有没算法或者去网上参考别人的算法，最后做一个时间性能比较。
<!--more-->
### 算法分析
看到这个题，当时想的就是很直接简单的方法，就是每次从牌组里随机抽取一张牌，如抽到空牌则再抽，放到一个新的牌组里，直到所有的牌都抽到新牌组就达到洗牌的效果，这种做法一共是n!种排法，每张牌在这n!种排法中概率都是一样的为1/n!;这样洗牌的随机性可以保证了；由于n-1个数都有可能随机到已抽取的，所以每张牌需要随机次数的平均值为O(n),则时间复杂度为O(n^2)。看下代码实现(以js为例)#`shuffle.js`:

``` javascript
var shuffle = function(cardsArr){
  var newArr = new Array(cardsArr.length);
  var tempArr = new Array(cardsArr.length);//记录已抽取的位置,1表示已抽取
  var position = Math.floor(Math.random()*cardsArr.length);
  for(var i=0; i<newArr.length; i++){
    while(tempArr[position] == 1){//直到抽取的随机数不为已抽取过的位置
      position = Math.floor(Math.random()*cardsArr.length);
    }
    newArr[i] = cardsArr[position];
    tempArr[position] = 1; //已抽取位置置1
  }
  return newArr;
};

// 测试
//注意这里如果直接遍历赋值当算法里面改动了cards的值时，无法进行循环多次测量(这里我打算测试10000次)，下面同理
var cards = function(){
  var arr = [];
  for(var i=0; i<54; i++){
    arr[i] = i+1;
  }
  return arr;
};

console.time("test time");
for(var i=0; i<10000; i++){
  shuffle(cards());
}
console.timeEnd("test time");

//输出结果为测试10000次，耗时基本落在420ms-440ms之间
```
这种写法会发现有两个不太合理的地方，一个是除了用来存新生成牌序的数组，还多申请了一个用来记录已抽过位置的数组内存，另一个是越抽到后面，空牌几率越大，需要重新生成随机数的次数就越多，特别是当牌组数量很大时，耗费很多系统时间。那如果把空牌剔除就没有这个问题，下面代码测试一下：
``` javascript
var shuffle = function(cardsArr){
  var newArr = new Array(cardsArr.length);
  for(var i=0; i<newArr.length; i++){
    var position = Math.floor(Math.random()*cardsArr.length);
    newArr[i] = cardsArr[position];
    cardsArr.splice(position, 1);//去除空牌
  }
  return newArr;
};

// 测试
var cards = function(){
  var arr = [];
  for(var i=0; i<54; i++){
    arr[i] = i+1;
  }
  return arr;
};

console.time("test time");
for(var i=0; i<10000; i++){
  shuffle(cards());
}
console.timeEnd("test time");

//输出结果为测试10000次，耗时基本落在400ms-420ms之间
```
这种方法不需要多申请一个状态数组，耗时也减少了，但对比第一种方法优化效果貌似不明显，主要在剔除空牌的时候，删除空牌后面的牌都需要相应进行前移，如果数组较大的话，是个很耗时的操作，因为数组移位的操作平均次数为n/2，时间复杂度仍为O(n^2),所以这个办法也不是很好。所以得想一个不需要整体前移的办法，可以把最后一张牌放置到要删除那张牌的位置上，这样不用删除直接覆盖掉，然后也不需要整体前移，只需要下次随机的时候不随到最后一张牌(虽然前移了，但该位置原值仍存在)，这样时间复杂度直接将为O(n)了，好的看下代码：
``` javascript
var shuffle = function(cardsArr){
  var newArr = [];
  var length = cardsArr.length;
  for(var i = length-1; i>=0; i--){
    var position = Math.floor(Math.random()*i);
    newArr[i] = cardsArr[position];
    cardsArr[position] = cardsArr[i];
  }
  return newArr;
};

// 测试
var cards = function(){
  var arr = [];
  for(var i=0; i<54; i++){
    arr[i] = i+1;
  }
  return arr;
};

console.time("test time");
for(var i=0; i<10000; i++){
  shuffle(cards());
}
console.timeEnd("test time");

//输出结果为测试10000次，耗时基本稳定在230ms左右
```
当然除了这种办法，在网上比较多的还是进行牌交换，达到牌间位置混淆的结果,这是一个叫Inside-Out Algorithm 算法，其基本思想是设一游标i从前向后扫描原始数据的拷贝，在[0, i]之间随机一个下标j，然后用位置j的元素替换掉位置i的数字，再用原始数据位置i的元素替换掉拷贝数据位置j的元素，其作用相当于在拷贝数据中交换i与j位置处的值;其时间复杂度为O(n),下面用js写一下：
``` javascript
var shuffle = function(cardsArr){
  for(var i=cardsArr.length-1; i>0; i--){
    var temp = cardsArr[i];
    var j = Math.floor(Math.random()*i);
    cardsArr[i] = cardsArr[j];
    cardsArr[j] = temp;
  }

  return cardsArr;
};

// 测试
var cards = function(){
  var arr = [];
  for(var i=0; i<54; i++){
    arr[i] = i+1;
  }
  return arr;
};

console.time("test time");
for(var i=0; i<10000; i++){
  shuffle(cards());
}
console.timeEnd("test time");

//输出结果为测试10000次，耗时基本在210-230ms
```

### 总结
通过以上算法分析，第一种方法优化后的和牌交换的算法比较好，它们的区别就是前者原始数据被打乱，如果遇到需要保留原数组的情况，就需要用一个数组来保存原始数据；通过网上的一些资料，才知道第一种方法的优化其实就是一个叫Knuth-Durstenfeld Shuffle的洗牌算法。