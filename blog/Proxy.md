---
title: Proxy
date: 2020-11-02 14:41:07
categories:
  - ES6
tags:
  - es6
---

### 概述

Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

```javascript
var obj = new Proxy(
  {},
  {
    get: function (target, propKey, receiver) {
      console.log(`getting ${propKey}!`);
      return Reflect.get(target, propKey, receiver);
    },
    set: function (target, propKey, value, receiver) {
      console.log(`setting ${propKey}!`);
      return Reflect.set(target, propKey, value, receiver);
    },
  }
);
```

上面代码对一个空对象架设了一层拦截，重定义了属性的读取（`get`）和设置（`set`）行为。这里暂时先不解释具体的语法，只看运行结果。对设置了拦截行为的对象`obj`，去读写它的属性，就会得到下面的结果。

```javascript
obj.count = 1;
//  setting count!
++obj.count;
//  getting count!
//  setting count!
//  2
```

上面代码说明，Proxy 实际上重载（overload）了点运算符，即用自己的定义覆盖了语言的原始定义。

ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。

```javascript
var proxy = new Proxy(target, handler);
```

Proxy 对象的所有用法，都是上面这种形式，不同的只是`handler`参数的写法。其中，`new Proxy()`表示生成一个`Proxy`实例，`target`参数表示所要拦截的目标对象，`handler`参数也是一个对象，用来定制拦截行为。

下面是另一个拦截读取属性行为的例子。

```javascript
let object = {};
var proxy = new Proxy(object, {
  get: function (target, propKey) {
    return 35;
  },
});

proxy.time; // 35
proxy.name; // 35
proxy.title; // 35
```

上面代码中，作为构造函数，`Proxy`接受两个参数。第一个参数是所要代理的目标对象（上例是一个空对象），即如果没有`Proxy`的介入，操作原来要访问的就是这个对象；第二个参数是一个配置对象，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作。比如，上面代码中，配置对象有一个`get`方法，用来拦截对目标对象属性的访问请求。`get`方法的两个参数分别是目标对象和所要访问的属性。可以看到，由于拦截函数总是返回`35`，所以访问任何属性都得到`35`。

注意，要使得`Proxy`起作用，必须针对`Proxy`实例（上例是`proxy`对象）进行操作，而不是针对目标对象（上例是空对象）进行操作。

如果`handler`没有设置任何拦截，那就等同于直接通向原对象。

```javascript
var target = {};
var handler = {};
var proxy = new Proxy(target, handler);
proxy.a = "b";
target.a; // "b"
```

上面代码中，`handler`是一个空对象，没有任何拦截效果，访问`proxy`就等同于访问`target`。

一个技巧是将 Proxy 对象，设置到`object.proxy`属性，从而可以在`object`对象上调用。

```javascript
var proxy = new Proxy(
  {},
  {
    get: function (target, propKey) {
      return 35;
    },
  }
);

let obj = Object.create(proxy);
obj.time; // 35
```

上面代码中，`proxy`对象是`obj`对象的原型，`obj`对象本身并没有`time`属性，所以根据原型链，会在`proxy`对象上读取该属性，导致被拦截。

同一个拦截器函数，可以设置拦截多个操作。

```javascript
var handler = {
  get: function (target, name) {
    if (name === "prototype") {
      return Object.prototype;
    }
    return "Hello, " + name;
  },

  apply: function (target, thisBinding, args) {
    return args[0];
  },

  construct: function (target, args) {
    return { value: args[1] };
  },
};

var fproxy = new Proxy(function (x, y) {
  return x + y;
}, handler);

fproxy(1, 2); // 1
new fproxy(1, 2); // {value: 2}
fproxy.prototype === Object.prototype; // true
fproxy.foo === "Hello, foo"; // true
```

对于可以设置、但没有设置拦截的操作，则直接落在目标对象上，按照原先的方式产生结果。

下面是 Proxy 支持的拦截操作一览，一共 13 种。

- **get(target, propKey, receiver)**：拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。
- **set(target, propKey, value, receiver)**：拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。
- **has(target, propKey)**：拦截`propKey in proxy`的操作，返回一个布尔值。
- **deleteProperty(target, propKey)**：拦截`delete proxy[propKey]`的操作，返回一个布尔值。
- **ownKeys(target)**：拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。
- **getOwnPropertyDescriptor(target, propKey)**：拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。
- **defineProperty(target, propKey, propDesc)**：拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。
- **preventExtensions(target)**：拦截`Object.preventExtensions(proxy)`，返回一个布尔值。
- **getPrototypeOf(target)**：拦截`Object.getPrototypeOf(proxy)`，返回一个对象。
- **isExtensible(target)**：拦截`Object.isExtensible(proxy)`，返回一个布尔值。
- **setPrototypeOf(target, proto)**：拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- **apply(target, object, args)**：拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。
- **construct(target, args)**：拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。

### Proxy.revocable()

`Proxy.revocable()`方法返回一个可取消的 Proxy 实例。

```javascript
let target = {};
let handler = {};

let { proxy, revoke } = Proxy.revocable(target, handler);

proxy.foo = 123;
proxy.foo; // 123

revoke();
proxy.foo; // TypeError: Revoked
```

`Proxy.revocable()`方法返回一个对象，该对象的`proxy`属性是`Proxy`实例，`revoke`属性是一个函数，可以取消`Proxy`实例。上面代码中，当执行`revoke`函数之后，再访问`Proxy`实例，就会抛出一个错误。

`Proxy.revocable()`的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。

### this 问题

虽然 Proxy 可以代理针对目标对象的访问，但它不是目标对象的透明代理，即不做任何拦截的情况下，也无法保证与目标对象的行为一致。主要原因就是在 Proxy 代理的情况下，目标对象内部的`this`关键字会指向 Proxy 代理。

```javascript
const target = {
  m: function () {
    console.log(this === proxy);
  },
};
const handler = {};

const proxy = new Proxy(target, handler);

target.m(); // false
proxy.m(); // true
```

上面代码中，一旦`proxy`代理`target`，`target.m()`内部的`this`就是指向`proxy`，而不是`target`。

下面是一个例子，由于`this`指向的变化，导致 Proxy 无法代理目标对象。

```javascript
const _name = new WeakMap();

class Person {
  constructor(name) {
    _name.set(this, name);
  }
  get name() {
    return _name.get(this);
  }
}

const jane = new Person("Jane");
jane.name; // 'Jane'

const proxy = new Proxy(jane, {});
proxy.name; // undefined
```

上面代码中，目标对象`jane`的`name`属性，实际保存在外部`WeakMap`对象`_name`上面，通过`this`键区分。由于通过`proxy.name`访问时，`this`指向`proxy`，导致无法取到值，所以返回`undefined`。

此外，有些原生对象的内部属性，只有通过正确的`this`才能拿到，所以 Proxy 也无法代理这些原生对象的属性。

```javascript
const target = new Date();
const handler = {};
const proxy = new Proxy(target, handler);

proxy.getDate();
// TypeError: this is not a Date object.
```

上面代码中，`getDate()`方法只能在`Date`对象实例上面拿到，如果`this`不是`Date`对象实例就会报错。这时，`this`绑定原始对象，就可以解决这个问题。

```javascript
const target = new Date("2015-01-01");
const handler = {
  get(target, prop) {
    if (prop === "getDate") {
      return target.getDate.bind(target);
    }
    return Reflect.get(target, prop);
  },
};
const proxy = new Proxy(target, handler);

proxy.getDate(); // 1
```

另外，Proxy 拦截函数内部的`this`，指向的是`handler`对象。

```javascript
const handler = {
  get: function (target, key, receiver) {
    console.log(this === handler);
    return "Hello, " + key;
  },
  set: function (target, key, value) {
    console.log(this === handler);
    target[key] = value;
    return true;
  },
};

const proxy = new Proxy({}, handler);

proxy.foo;
// true
// Hello, foo

proxy.foo = 1;
// true
```

上面例子中，`get()`和`set()`拦截函数内部的`this`，指向的都是`handler`对象。
