---
categories : [翻译]
date: 2020-03-28
tags : [JavaScript]
title: Immutability in JavaScript
---

> 原文：[Immutability in JavaScript](https://yvonnickfrin.dev/immutability-in-javascript)

根据维基百科，不可变对象（不可更改对象）是创​​建后状态无法修改的对象。该规则非常简单，如果要修改对象的某些属性，则必须在副本上进行操作。稍后我们将看到它为我们的开发带来了哪些改进和精美功能。

<!--more-->

## EcmaScript

EcmaScript 提供了实用程序来使我们的数据保持不变。数组和对象 API 包含创建副本，甚至阻止实例更新的方法。最近，EcmaScript 引入了一些新的语法来创建对象和数组的副本。

### Object.assign

我们想在对象中添加一个name属性。

```js
const lutraLutra = {
  commonNames: ['eurasian otter'],
}
```

我们可以使用 `Object.assign` 和一些技巧来做到这一点。基本上，它将所有属性从一个对象复制到另一个对象，从而使目标对象发生改变。我们使用一个小技巧，将一个空对象作为第一个参数，这将创建一个新引用。

```js
const newLutraLutra = Object.assign(
  {}, 
  lutraLutra,
  { name: 'Lutra lutra' },
)
```

现在，我们有了一个具有新 `name` 属性的新对象，并且 `commonNames` 属性保持不变。使用此方法，您可以同时创建/覆盖多个属性。

<!--more-->

### Array.concat

现在，让我们用数组来做。我们希望以不变的方式在数组中添加两个新元素。

```js
const commonNames = ['eurasian otter']
```

与 `Array.push` 不同，`Array.concat` 不会使我们的数组发生改变。而是返回一个新数组。

```js
const newCommonNames = commonNames.concat(
  'european otter',
  'common otter'
)
```

这种方法很灵活。它可以接受一个或多个元素。它们可以是值或数组。

### Object.freeze

`Object.freeze` 并不是很熟悉。它使对象不可变！它可以防止各种类型的改变（创建，修饰，删除）。

```js
let lutraLutra = {
  commonNames: ['eurasian otter', 'european otter', 'common otter'],
  name: 'Lutra lutra'
}
```

冻结对象后，我们将尝试删除 `name` 属性。

```js
lutraLutra = Object.freeze(lutraLutra)
delete lutraLutra.name
```

使用 `Object.freeze` 使作为参数传递的对象不可变。此方法有两种可用模式：
* 非严格模式下，改变没效果
* 严格模式下，如果您尝试修改，则会引发 TypeError

**注意，它不是递归的。我们的属性 `commonNames` 不是不可改变的。**

### Spread operator

ES2015 在数组中引入了扩展运算符语法，ES2018 也在对象中引入。它将给定对象的所有属性复制到新的对象文字中。

```js
const newLutraLutra = {
  ...lutraLutra,
  name: 'Lutra lutra',
}
```

使用数组时，它将数组的所有值复制到新数组中。

```js
const newCommonNames = [
  ...commonNames,
  'common otter',
]
```

它很好地替换了 `assign` 和 `concat`，易于阅读。

## 为什么要使用不变性？

你了解了如何使用 JavaScript 使对象和数组不可变，但是我们至今仍未解释为什么为什么必须使用不可变性。作为开发人员，我们一直在寻找编写更可维护和可读的代码的方法。一些诸如函数式编程之类的范例正专注于此。

> 函数式编程的目标是使我们减少思考，编写更多描述性代码。
>
> -[Alexander Kondov](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-immutability-4e9751ca005c)

它具有声明式的编程方法，这意味着您将重点放在描述程序必须完成的事情上，而不是程序应该如何完成。它为您的代码赋予了更多含义，以便下一个开发人员可以更轻松地理解它。函数式编程带来了其他有助于实现此目标的概念，例如不变性。

### 好处是什么？

这听起来像是炒作术语吗？不可变性为我们每天遇到的编程问题带来了许多解决方案：

* 避免副作用
* 数据更改检测变得简单（浅比较）
* 显式数据更改
* 记忆化
* 内存优化
* 更好的渲染性能
* 易于测试

> 与 JavaScript 世界中的大多数趋势不同，数据不变性必然会在一段时间内停留在我们身上，这有充分的理由：首先，因为这不是趋势，这是一种编码（和思考代码）的方式，可以提高清晰度，简化操作使用和理解数据流，并使代码不易出错。
>
> -[Ricardo Magalhães](https://hackernoon.com/data-immutability-with-vanilla-javascript-63834a65a6c9)

在过去的几年中，我们最大的挑战之一是找到一种有效的方法来检测数据中的变化，以确定是否应该渲染接口。检测原始值之间的变化很容易，但是对于对象和数组则完全不同。您可以按值比较它们，但必须实施递归算法并处理诸如循环引用之类的潜在问题。另一种方法是将对象引用与严格相等运算符===进行比较。它更有效，并且没有进入任何致命的无限循环的风险。因此，现代框架实施了这一概念。

### 被现代框架/库重视

现代的前端框架和库基于可大大提高性能的概念。这是众所周知的虚拟 DOM。该技术是根据简单的证据创建的：DOM 操作很昂贵。

如前所述，前端框架和库选择使用不变性以提高其性能。如今，我们必须在应用程序中处理越来越多的数据，因此需要处理更多的标记。因此，我们的浏览器需要比10年前处理更多的计算。DOM 操作很昂贵，现代框架倾向于减少 DOM 更新的数量。

### 为什么我们需要实用程序库？

正如我们前面所看到的，由于使用了语法糖，因此在 `EcmaScript` 中处理不变性的方法很简单，但是在嵌套结构中却非常有限。随着像 `redux` 这样的库的出现，嵌套结构变得越来越流行。

```js
const animals = {
  weasels: {
    lutraLutra: {
      commonNames: ['eurasian otter'],
    },
  },
}
const newAnimals = {
  ...animals,
  weasels: {
    ...animals.weasels,
    lutraLutra: {
      ...animals.weasels.otter,
      name: 'Lutra lutra',
    },
  },
}
```

如您所见，编写变得更加乏味且难以阅读。简单的用例（如覆盖数组的索引）很难实现。

```js
const lutraLutra = {
  name: 'Lutra lutra',
  commonNames: ['eurasian otter', 'european', 'common otter'],
}
const newCommonNames = [...lutraLutra.commonNames]
newCommonNames[1] = 'european otter'
const newLutraLutra = { 
  ...lutraLutra,
  commonNames: newCommonNames,
}
```

这些原因足以开始寻找一些工具，帮助您专注于真正重要的事情，即代码的含义。这就是我们创建 `immutadot` 的原因，以帮助我们保持 `javascript` 代码库的可读性和可维护性。[试试看](https://github.com/zenika-open-source/immutadot)。

