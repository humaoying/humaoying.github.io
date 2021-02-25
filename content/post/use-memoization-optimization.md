---
date: 2018-09-17
categories : [前端]
tags : [JavaScript]
title: 使用 Memoization 优化代码性能
---

**memoization 来源于拉丁语 memorandum ("to be remembered")，不要与 memorization 混淆了。**

首先来看一下维基百科的描述：

> In computing, memoization or memoisation is an optimization technique used primarily to speed up computer programs by storing the results of expensive function calls and returning the cached result when the same inputs occur again.

<!--more-->

简单来说，memoization 是一种优化技术，主要用于通过存储昂贵的函数调用的结果来加速计算机程序，并在再次发生相同的输入时返回缓存的结果。

本文首先介绍一个简单的使用 memoization 优化技术的例子，然后解读 underscore 和 reselect 库中使用 memoization 的源码，加深理解。

## 阶乘

### 不使用 memoization

不假思索，我们会立即写下如下的代码：

```js
const factorial = n => {
    if (n === 1) {
        return 1
    } else {
        return factorial(n - 1) * n
    }
};
```

<!-- more -->

### 使用 memoization

```js
const cache = []
const factorial = n => {
    if (n === 1) {
        return 1
    } else if (cache[n - 1]) {
        return cache[n - 1]
    } else {
        let result = factorial(n - 1) * n
        cache[n - 1] = result
        return result
    }
};
```

### 使用 闭包 和 memoization

常见的方式是 闭包 和 memoization 一起搭配使用：

```js
const factorialMemo = () => {
    const cache = []
    const factorial = n => {
        if (n === 1) {
            return 1
        } else if (cache[n - 1]) {
            console.log(`get factorial(${n}) from cache...`)
            return cache[n - 1]
        } else {
            let result = factorial(n - 1) * n
            cache[n - 1] = result
            return result
        }
    }
    return factorial
};
const factorial = factorialMemo();
```

**继续变形,下面这种编写方式是最常见的形式。**


```js
const factorialMemo = func => {
    const cache = []
    return function(n) {
        if (cache[n - 1]) {
            console.log(`get factorial(${n}) from cache...`)
            return cache[n - 1]
        } else {
            const result = func.apply(null, arguments)
            cache[n - 1] = result
            return result
        }
    }
}

const factorial = factorialMemo(function(n) {
    return n === 1 ? 1 : factorial(n - 1) * n
});
```

从阶乘的这个例子可以知道 memoization 是一个空间换时间的方式，存储执行结果，下次再次发生相同的输入会直接输出结果，提高了执行的速度。

## underscore 源码中的 memoization

```js
// Memoize an expensive function by storing its results.
_.memoize = function(func, hasher) {
    var memoize = function(key) {
        var cache = memoize.cache;
        var address = '' + (hasher ? hasher.apply(this, arguments) : key);
        if (!_.has(cache, address)) cache[address] = func.apply(this, arguments);
        return cache[address];
    };
    memoize.cache = {};
    return memoize;
};
```

代码一目了然，使用 \_.memoize 来实现阶乘如下：

```js
const factorial = _.memoize(function(n) {
    return n === 1 ? 1 : factorial(n - 1) * n
});
```

参照这个源码，上面的阶乘继续可以变形如下：

```js
const factorialMemo = func => {
    const memoize = function(n) {
        const cache = memoize.cache
        if (cache[n - 1]) {
            console.log(`get factorial(${n}) from cache...`)
            return cache[n - 1]
        } else {
            const result = func.apply(null, arguments)
            cache[n - 1] = result
            return result
        }
    }
    memoize.cache = []
    return memoize
}

const factorial = factorialMemo(function(n) {
    return n === 1 ? 1 : factorial(n - 1) * n
});
```

## reselect 源码中的 memoization

```js
export function defaultMemoize(func, equalityCheck = defaultEqualityCheck) {
    let lastArgs = null
    let lastResult = null
    // we reference arguments instead of spreading them for performance reasons
    return function () {
        if (!areArgumentsShallowlyEqual(equalityCheck, lastArgs, arguments)) {
            // apply arguments instead of spreading for performance.
            lastResult = func.apply(null, arguments)
        }

        lastArgs = arguments
        return lastResult
    }
};
```

从源码可以知道当 lastArgs 与 arguments 相同的时候，就不会再执行 func。

## 总结

memoization 是一种优化技术，避免一些不必要的重复计算，可以提高计算速度。


## 参考

1. [Memoization wiki](https://en.wikipedia.org/wiki/Memoization)
2. [Understanding JavaScript Memoization In 3 Minutes](https://codeburst.io/understanding-memoization-in-3-minutes-2e58daf33a19)
3. [Underscore](https://github.com/jashkenas/underscore)
4. [reselect](https://github.com/reduxjs/reselect)
5. [Implementing Memoization in JavaScript](https://www.sitepoint.com/implementing-memoization-in-javascript/)

