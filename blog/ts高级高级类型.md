---
title: TypeScript 中的高级类型
date: 2020-09-13 13:11:56
categories:
  - TypeScript
tags:
  - TypeScript
---

### 交叉类型

交叉类型是将多个类型合并为一个类型，这让我们可以把现有的多种类型叠加到一起作为一种类型，它包含了所需的所有类型的特性，例如`Person & Serializable & Loggable `同时是 `Person` 和 `Serializable` 和 `Loggable`。 就是说这个类型的对象同时拥有了这三种类型的成员。

我们大多是在混入或其他不适合经典面向对象的地方看到交叉类型的使用，（在`JavaScript`中发生这种情况的场合会很多）下面是如何创建混入的一个简单的例子:

```typescript
function extend<T, U>(first: T, second: U): T & U {
  let result = <T & U>{};
  for (let id in first) {
    (<any>result)[id] = (<any>first)[id];
  }
  for (let id in second) {
    if (!result.hasOwnProperty(id)) {
      (<any>result)[id] = (<any>second)[id];
    }
  }
  return result;
}

class Person {
  constructor(public name: string) {}
}
interface Loggable {
  log(): void;
}
class ConsoleLogger implements Loggable {
  log() {
    // ...
  }
}
var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();
```

<!-- more -->

### 联合类型

联合类型与交叉类型很有关联，但是使用上却完全不同，偶尔你会遇到这种情况，一个代码库希望传入`number`或`string`类型作为参数，例如下面的函数

```typescript
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: any) {
  if (typeof padding === "number") {
    return Array(padding + 1).join(" ") + value;
  }
  if (typeof padding === "string") {
    return padding + value;
  }
  throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"
```

现在 ` padLeft` 存在一个问题，`padding` 参数的类型指定为了 `any` ，这就是说我们可以传入一个既不是`number` 也不是 `string` 类型的参数，但是`typescript`却不报错，

```typescript
let indentedString = padLeft("Hello world", true); // 编译阶段通过，运行时报错
```

在传统的面向对象语言里，我们可能会将这两种抽象成底层的类型，这么做显然是非常清晰的，但同时也存在了过度设计，`padleft` 原始版本的好处之一就是允许我们呢出传入原始类型，这样做的话用起来简单又方便，如果我们就是想使用已经存在的函数的话，这种新的方式就不适用了。

代替`any`，我们可以使用联合类型作为 `padding` 的参数

```typescript
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: string | number) {
  // ...
}

let indentedString = padLeft("Hello world", true); // errors during compilation
```

联合类型表示一个值可以是几种类型之一，我们用竖线（`|`）分割每个类型，所以`number|string|boolean`，表示值可以是`number` `string` 或 `boolean`

如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员

```typescript
interface Bird {
  fly();
  layEggs();
}

interface Fish {
  swim();
  layEggs();
}

function getSmallPet(): Fish | Bird {
  // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim(); // errors
```

这里的联合类型可能有点复杂，但是你很容易就习惯了。 如果一个值的类型是 `A | B`，我们能够 确定的是它包含了 `A` 和 `B` 中共有的成员。 这个例子里， `Bird`具有一个 `fly`成员。 我们不能确定一个 `Bird | Fish`类型的变量是否有 `fly`方法。 如果变量在运行时是 `Fish` 类型，那么调用 `pet.fly()` 就出错了。

### 类型保护与区分类型

联合类型适合于那些值可以作为不同类型的情况，但当我们想确切的了解是否为 `Fish` 时怎么办？ `JavaScript` 里常用来区分两个可能的值的方法是检查成员是否存在，如之前提及的，我们只能访问联合类型中共同拥有的成员

```typescript
let pet = getSmallPet();

// 每一个成员访问都会报错
if (pet.swim) {
  pet.swim();
} else if (pet.fly) {
  pet.fly();
}
```

为了让这段代码工作，我们需要使用类型断言

```typescript
let pet = getSmallPet();

if ((<Fish>pet).swim) {
  (<Fish>pet).swim();
} else {
  (<Bird>pet).fly();
}
```

**用户自定义的类型保护**

这里可以注意到，我们不得不多次使用类型断言，假如我们一旦检查过类型，就能在之后的每个分支里清楚的知道 `pet` 的类型的话就好了

这样 `TypeScript` 里的 类型保护机制让它成为了现实。 类型保护就是一些表达式，它们会在运行时检查以确保在某个作用域里的类型。 要定义一个类型保护，我们只要简单地定义一个函数，它的返回值是一个 _类型谓词_：

```typescript
function isFish(pet: Fish | Bird): pet is Fish {
  return (<Fish>pet).swim !== undefined;
}
```

在这个例子里， `pet is Fish`就是类型谓词。 谓词为 `parameterName is Type` 这种形式， `parameterName` 必须是来自于当前函数签名里的一个参数名。

每当使用一些变量调用 `isFish` 时，`TypeScript` 会将变量缩减为那个具体的类型，只要这个类型与变量的原始类型是兼容的。

```typescript
// 'swim' 和 'fly' 调用都没有问题了

if (isFish(pet)) {
  pet.swim();
} else {
  pet.fly();
}
```

注意`typescript`不仅知道在`if`分支里 `pet` 是 `Fish` 类型的，它还清楚在 `else` 分支里，一定不是 `Fish` 类型的，一定是 `Bird` 类型的
