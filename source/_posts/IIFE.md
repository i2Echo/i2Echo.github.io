---
title: 从函数到函数表达式（FE）的深入理解（JavaScript）
tags: [js, IIFE, 函数, 函数表达式, 自执行函数表达式]
date: 2017-01-02 01:10:04
categories: [Javascript]
---
本文主要针对自己学习过程中对ECMAScript对象-函数的一些模糊理解的地方或者一些高级用法采用笔记的方式写下来，方便进一步理解以及复习。
<!--more-->

## 函数类型
ECMAScript中主要包含三类函数，每一类都有各自的特性。

### 函数声明（Function Declaration）
#### 特性（Features）
* 有函数名
* 代码位置在：要么在程序级别或者直接在另外一个函数的函数体（FunctionBody）中
* 在进入上下文时创建出来的
* 会影响变量对象
* 形式如下：
``` javascript
function foo() {
  //...
}
```
**Tips**
> 这类函数的主要特性是：只有它们可以影响变量对象（存储在上下文的VO中）;此特性同时也引出了非常重要的一点（变量对象的天生特性导致的） —— 它们在执行代码阶段就已经存在了（因为FD在进入上下文阶段就收集到了VO中）.

### 函数表达式（Function Expression）
#### 特性（Features）
* 代码位置必须要在表达式的位置
* 名字可有可无（无名的我们就叫匿名函数）
* 不会影响变量对象
* 在执行代码阶段创建出来
* 最简单常见的就是赋值表达式
``` javascript
var foo = function() {
  //...
}
//通过变量foo就可以使用函数了
// 注意：函数声明式也会出现变量提升的问题，但是函数表达式不会出现,所以这里不能先调用，必须声明在前。
```
* 当然函数表达式也可以有名字
``` javascript
var foo = function _foo(){
//...
}
// 在内部使用时（如递归调用）也可以直接使用_foo
```
#### FE的一些使用
* 定义中可以看到这类函数出现的位置是表达式，那就可以通过这个区分一般的函数声明了：
``` javascript
// 在括号中(grouping operator)只可能是表达式
(function foo() {});
 
// 在数组初始化中 —— 同样也只能是表达式
[function bar() {}];
 
// 逗号操作符也只能跟表达式
1, function func() {};
```
* 定义中也说到FE是在代码执行阶段创建所以未执行前使用时是未定义的：
``` javascript
// 不论是在定义前还是定义后，FE都是无法访问的
// (因为它是在代码执行阶段创建出来的),
 
console.log(foo); // "foo" is not defined
 
(function foo() {});
 
// 后面也没用，因为它根本就不在变量对象中
console.log(foo);  // "foo" is not defined
```
那么要怎么去调用呢？无论你定义一个函数像这样function foo(){}或者var foo = function(){}，调用时，你都需要在后面加上一对圆括号，像这样foo()或者直接在表达式后面加上括号也是可以的，但是要注意的是只能在函数表达式后面加括号，如果是函数声明会报错的，那怎么区别呢，前面说到通过出现的位置去判断是否能使用括号。当然还有一种情况，“当圆括号包裹函数时，它会默认将函数作为表达式去解析，而不是函数声明。”，例如：(function(){/* code */}());这样使用是不会报错的，这里的匿名函数是当成函数表达式解析的。

#### 立即执行函数表达式（Immediately-Invoked Function Expression）
上面说到通过括号执行函数表达式，那么就像这样(function foo() {})();看到这个是不是很熟悉，没错，这就是我们常用的立即执行函数表达式，在一些前端库开头经常会看到这种用法。
* IIFE的一些形式
``` javascript
//下面是个自执行函数，递归的调用自己本身

function foo(){foo();};

//这是一个自执行匿名函数。因为它没有标识符，它必须是使用`arguments.callee`属性来调用它自己

var foo = function(){arguments.callee();};

//这也许算是一个自执行匿名函数，但是仅仅当`foo`标识符作为它的引用时，如果你将它换成用`foo`来调用同样可行

var foo = function(){foo();};

//有些人像这样叫'self-executing anonymous function'下面的函数,即使它不是自执行的，因为它并没有调用它自己。然后，它只是被立即调用了而已。

(function(){ /*code*/ }());

//为函数表达式增加标识符(也就是说创造一个命名函数)对我们的调试会有很大帮助。一旦命名，函数将不再匿名。

(function foo(){/* code */}());

//IIFEs同样也可以自执行，尽管，也许他不是最有用的模式

(function(){arguments.callee();}())
(function foo(){foo();}())

```
* IIFE的闭包特性
IIFE会保存闭包的状态，就像函数通过他们的名字被调用时，参数会被传递，而当函数表达式被立即调用时，参数也会被传递。一个立即调用的函数表达式可以用来锁定值并且有效的保存此时的状态，因为任何定义在一个函数内的函数都可以使用外面函数传递进来的参数和变量(这种关系被叫做闭包)；一个常见的获取当前点击元素的索引值的例子：
``` javascript
/** 
  它的运行结果并不像你想的那样，因为`i`的值从来没有被锁定。
  事实上，每个链接，当被点击时（循环已经执行完毕），因此会弹出所有元素的总数，
  因为这是 `i` 此时的真实值。
*/
var elems = document.getElementsByTagName('a');
for(var i = 0;i < elems.length; i++ ) {
    elems[i].addEventListener('click',function(e){
        e.preventDefault();
        alert('I am link #' + i)
    },false);
}

/**
  而像下面这样改写，便可以了，因为在IIFE里，`i`值被锁定在了`lockedInIndex`里。
  在循环结束执行时，尽管`i`值的数值是所有元素的总和，但每一次函数表达式被调用时，
  IIFE 里的 `lockedInIndex` 值都是`i`传给它的值,所以当链接被点击时，正确的值被弹出。
*/
var elems = document.getElementsByTagName('a');
for(var i = 0;i < elems.length;i++) {
    (function(lockedInIndex){
        elems[i].addEventListener('click',function(e){
            e.preventDefault();
            alert('I am link #' + locked InIndex);
            },false)
    })(i);
}

/**
  你同样可以像下面这样使用IIFE，仅仅只用括号包括点击处理函数，并不包含整个`addEventListener`。
  无论用哪种方式，这两个例子都可以用IIFE将值锁定，不过我发现前面一个例子更可读
*/
var elems = document.getElementsByTagName( 'a' );

for ( var i = 0; i < elems.length; i++ ) {
    elems[ i ].addEventListener( 'click', (function( lockedInIndex ){
        return function(e){
            e.preventDefault();
            alert( 'I am link #' + lockedInIndex );
        };
        })( i ),false);
    }

```

#### 函数表达式的模块模式
``` javascript
var counter = (function(){
  var i = 0;
  return{
    get: function(){
        return i;
    },
    set: function(val){
        i = val;
    },
    increment: function(){
        return ++i;
    }
  }
}());

counter.set(1);
counter.get();//1
counter.increment();//2
counter.increment();//3
```
模块模式方法的好处显而易见，简洁明了，非常少的代码；你可以有效的利用与方法和属性相关的命名，在一个对象里，组织全部的模块代码即最小化了全局变量的污染也创造了使用变量，在一些js库中也很常见。

### Function构造器声明的函数
#### 特性（Features）
* Function 构造函数 创建一个新的Function对象。
* 在 JavaScript 中, 每个函数实际上都是一个Function对象。
* 使用Function构造器生成的Function对象是在函数创建时解析的。
* **注意:** 使用Function构造器生成的函数，并不会在创建它们的上下文中创建闭包；它们一般在全局作用域中被创建。当运行这些函数的时候，它们只能访问自己的本地变量和全局变量，不能访问Function构造器被调用生成的上下文的作用域
* 形式如下：
``` javascript
// 创建了一个能返回两个参数和的函数
const adder = new Function("a", "b", "return a + b");

adder(1, 2); //3
```
因为这里不是主要去讲函数的构造器就不做过多详细说明。

## 参考文献
* [Immediately-Invoked Function Expression (IIFE)](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)
* [Function - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function)