---
title: 'JS tips #1'
date: 2016-08-14 19:51:43
tags: JS
---

## `Function.length`

有一天在看 Vue.js 的 [tutorial](https://vuejs.org/guide/transitions.html#Transition-Flow-Details)
發現了一個很不得了的事情, 裡面有一個 function 會吃一個使用者自訂的 callback function.
神奇的事情是他會根據傳進來 function 的 signature 判斷來做不同的處理!  
有點像
```js
const f = (fun) => { ... }
f(() => {})
// output "function with no arguments"
f((a) => {})
// output "function with one arguments"
```

<!--more-->

查了一下才發現 ES 的 function 有個 `Function.length` 的屬性, 會回傳有幾個必要引數.
```js
const f0 = () => {},            // f0.length = 0
      f1 = (a) => {},           // f1.length = 1
      f2 = (a, b) => {},        // f2.length = 2
      g  = (...args) => {},     //  g.length = 0
      h  = (a, b=0, c) => {};   //  h.length = 1, 只算有 default value 之前的
```

## `Array.prototype.sort()`
ES 內建的 `sort` 非常雷,
```js
[1, 2, 3, 10, 11].sort() // [1, 10, 11, 2, 3]
```
他會全部轉成字串 (`.toString()`) 之後來排序.
如果希望照著**正常**的方法排序, 用
```js
[1, 2, 3, 10, 11].sort((x, y) => x-y);
```
也就是說 `sort` 可以額外傳一個 compare function, 小於、等於和大於分別回傳負數、0 和正數. 

## Method definitions shorthand
ES6 中提供了一個縮寫
```js
let obj = {
    foo: 'bar',
    fun() { console.log(this) },
};
// same as
let obj = {
    foo: 'bar',
    fun: function() { console.log(this) },
};
```
