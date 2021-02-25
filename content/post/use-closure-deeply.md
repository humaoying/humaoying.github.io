---
date: 2019-11-06
updated: 2019-11-06
categories : [前端]
tags : [JavaScript]
title: 闭包在 curry/compose/memo 中的使用
draft: true
---

什么是闭包？

> Douglas Crockford 在 《JavaScript：The Good Parts》中说：一个内部函数除了可以访问自己的参数和变量，同时它也能自由访问把它嵌套在其中的父函数的参数和变量。

**简单来说：闭包就是携带状态的函数，并且它的状态可以完全对外隐藏起来。**

<!--more-->


## curry

**curry: 只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数**

```js
function curry(func) {
    const len = func.length
    function judgeCurry() {
        const arg = arguments
        if (arguments.length >= len) {
            return func.apply(null, arg)
        } else {
            return function() {
                return judgeCurry.call(null, ...arg, ...arguments)
            }
        }
    }
    return judgeCurry
}

// 测试

const add = (a, b, c) => a + b + c

const curryAdd = curry(add)

console.log(curryAdd(1)(2)(3))
console.log(curryAdd(1)(2,3))
console.log(curryAdd(1,2,3))
```


## compose

```js
const compose = function() {
    const len = arguments.length
    const args = [].slice.call(arguments)
    return function() {
        const initValue = args[len - 1].apply(null, arguments)
        const remainFunctions = args.slice(0, len - 1).reverse()
        return remainFunctions.reduce(function(previousValue, currentValue) {
            return currentValue(previousValue)
        }, initValue)
    }
}


// 测试

const display = x => console.log(x)
const toUpperCase = x => x.toUpperCase()
const addPrefix = x => `prefix_${x}`
const addSuffix = x => `${x}_suffix`
const fixWord = compose(display, addPrefix, addSuffix, toUpperCase)
fixWord('hello world')

```

## memo

```js
function memo(func) {
    const memoize = function(key) {
        const cache = memoize.cache
        console.log(cache.hasOwnProperty(key))
        if (!cache.hasOwnProperty(key)) {
            cache[key] = func.apply(null, arguments)
        }
        return cache[key]
    }
    // 绑定到闭包函数上，不会造成内存泄露
    memoize.cache = {}
    return memoize
}

// 测试

const fibonacci = n => {
    if (n === 1 || n === 2) {
        return 1
    } else {
        return fibonacci(n - 1) + fibonacci(n - 2)
    }
}

const memoFibonacci = memo(fibonacci)

console.log(memoFibonacci(10))
console.log(memoFibonacci(10))
```
