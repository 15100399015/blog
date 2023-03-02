---
title: Completion Record
date: 2020-11-21 11:55:05
tags:
---

JavaScript 用语句来完成流程控制

```javascript
if (x === 1) return 10;
```

这是一条简单的 `if` 语句，它的完成状态可能是不一样的，`return` 有可能会执行也有可能不会执行取决于 x 变量具体的值，

所以 JavaScript 引擎在解析`if`语句的时候就需要知道它完成之后的结果到底是怎样的。于是在 JavaScript 语言中就需要一种数据结构来存储语句的完成结果，这就是我们所谓的 Completion Record 类型了，它不在其中基本类型中，我们在 JavaScript 中无论如何都是无法获取到这个数据，它没有办法赋值给变量也没有办法作为参数，但是它确确实实存在于 JavaScript 的运行时，

每写一条一句就会产生 Completion Record 这样的东西，

比如上面的`if`语句，它的 Completion Record 可能包含了这些信息：是否返回了？返回值是啥？等等.....

接下来我们就来看看一个具体的 Completion Record 是怎样组成的

```text
[[type]]: normal, break，continue，return，or throw
[[value]]: 基本类型值
[[target]]：label
```

type 部分是它的类型，正常的语句它都是 normal，有些语句可能会产生 break，continue，return，or throw，type 是一个非常关键的信息，

值得注意的是，比如说我们的`if`语句或者说我们`for`循环，它里面都有可能产生 `return`，`break`，`continue`，这些穿透力比较强的 type 可能会改变父语句的 type

```javascript
// 函数被直接返回了
function foo() {
  if (true) {
    return true;
  }
}
```

value：有些语句，尤其是表达式语句，他一定是有一个返回值的，正因为它有返回值，所以它才能发挥一定的作用，另外 return，throw，它都会带着一个值，这个值也是控制中的一个关键，这个值可能会被抛给其他的语句或者是赋值给一些变量去执行，所以 value 也是 Completion Record 里面一个重要的信息

target：这个非常少见 target 是一个 label，这个东西其实就是我们在语句前面加上一个标识符和冒号，然后这样的话这个语句就变成了一个带 label 的语句。

```javascript
// label 可以和break，continue 配合使用达到一些如跳出指定循环的效果
outPoint: for (let i = 0; i < 100; i++) {
  for (let j = 0; j < 10; j++) {
    console.log(i, j);
    if (i === 55 && j === 5) {
      break outPoint;
    }
  }
}
```

以上就是 JavaScript 中的 Completion Record 了，我们单看 Completion Record，其实他就是一个语句完成状态的这样的一个记录
