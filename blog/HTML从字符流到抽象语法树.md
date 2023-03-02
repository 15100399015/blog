---
title: HTML从字符流到抽象语法树
date: 2020-12-07 14:27:44
tags:
---

词法分析，语法分析，状态机，米利状态机，JS实现状态机

```JavaScript
// lexer.js 词法分析
const EOF = void 0;

// html词法分析器， 接受一个语法解析器
function HTMLLexicalParser(syntaxer) {
  // 状态
  let state = data;

  // 存储
  let token = null;
  let attribute = null;
  // 接收输入
  this.receiveInput = function (char) {
    if (state == null) {
      throw new Error("there is an error");
    } else {
      // 更新状态
      state = state(char);
    }
  };

  this.reset = function () {
    state = data;
  };

  // 初始状态判断
  function data(c) {
    if (c === "<") {
      return tagOpen; // 标签开始
    }
    emitToken(c);
    return data;
  }

  // 标签开始状态
  function tagOpen(c) {
    // 标签结束
    if (c === "/") {
      return endTagOpen;
    }
    // 是否为任意大小写字母
    if (/[a-zA-Z]/.test(c)) {
      token = new StartTagToken();
      token.name = c.toLowerCase();
      return tagName;
    }

    return error(c);
  }
  // 标签名状态
  function tagName(c) {
    if (c === "/") {
      return selfClosingTag;
    }
    if (/[\t \f\n]/.test(c)) {
      return beforeAttributeName;
    }
    if (c === ">") {
      emitToken(token);
      return data;
    }
    if (/[a-zA-Z]/.test(c)) {
      token.name += c.toLowerCase();
      return tagName;
    }
  }
  // 等待属性名
  function beforeAttributeName(c) {
    if (/[\t \f\n]/.test(c)) {
      return beforeAttributeName;
    }
    if (c === "/") {
      return selfClosingTag;
    }
    if (c === ">") {
      emitToken(token);
      return data;
    }
    if (/["'<]/.test(c)) {
      return error(c);
    }

    attribute = new Attribute();
    attribute.name = c.toLowerCase();
    attribute.value = "";
    return attributeName;
  }
  // 属性名
  function attributeName(c) {
    if (c === "/") {
      token[attribute.name] = attribute.value;
      return selfClosingTag;
    }
    if (c === "=") {
      return beforeAttributeValue;
    }
    if (/[\t \f\n]/.test(c)) {
      return beforeAttributeName;
    }
    attribute.name += c.toLowerCase();
    return attributeName;
  }
  // 等待属性内容
  function beforeAttributeValue(c) {
    if (c === '"') {
      return attributeValueDoubleQuoted;
    }
    if (c === "'") {
      return attributeValueSingleQuoted;
    }
    if (/\t \f\n/.test(c)) {
      return beforeAttributeValue;
    }
    attribute.value += c;
    return attributeValueUnquoted;
  }

  // 属性内容双引号
  function attributeValueDoubleQuoted(c) {
    if (c === '"') {
      token[attribute.name] = attribute.value;
      return beforeAttributeName;
    }
    attribute.value += c;
    return attributeValueDoubleQuoted;
  }
  // 属性内容单引号
  function attributeValueSingleQuoted(c) {
    if (c === "'") {
      token[attribute.name] = attribute.value;
      return beforeAttributeName;
    }
    attribute.value += c;
    return attributeValueSingleQuoted;
  }
  // 属性内容
  function attributeValueUnquoted(c) {
    if (/[\t \f\n]/.test(c)) {
      token[attribute.name] = attribute.value;
      return beforeAttributeName;
    }
    attribute.value += c;
    return attributeValueUnquoted;
  }
  // 自封闭
  function selfClosingTag(c) {
    if (c === ">") {
      emitToken(token);
      endToken = new EndTagToken();
      endToken.name = token.name;
      emitToken(endToken);
      return data;
    }
  }
  // 结束标签打开
  function endTagOpen(c) {
    if (/[a-zA-Z]/.test(c)) {
      token = new EndTagToken();
      token.name = c.toLowerCase();
      return tagName;
    }
    if (c === ">") {
      return error(c);
    }
  }
  // 发出Token
  function emitToken(token) {
    syntaxer.receiveInput(token);
  }
  function error(c) {
    console.log(`warn: unexpected char '${c}'`);
  }
}

class StartTagToken {}

class EndTagToken {}

class Attribute {}

module.exports = {
  HTMLLexicalParser,
  StartTagToken,
  EndTagToken,
};
```
```JavaScript
// syntaxer.js 语法分析
const { StartTagToken, EndTagToken } = require("./lexer");

class HTMLDocument {
  constructor() {
    this.isDocument = true;
    this.childNodes = [];
  }
}
// 根node节点
class Node {}
// 元素节点
class Element extends Node {
  constructor(token) {
    super(token);
    for (const key in token) {
      this[key] = token[key];
    }
    this.childNodes = [];
  }
  [Symbol.toStringTag]() {
    return `Element<${this.name}>`;
  }
}
// 文本节点
class Text extends Node {
  constructor(value) {
    super(value);
    this.value = value || "";
  }
}
// html 分析器
function HTMLSyntaticalParser() {
  const stack = [new HTMLDocument()];

  this.receiveInput = function (token) {
    if (typeof token === "string") {
      if (getTop(stack) instanceof Text) {
        getTop(stack).value += token;
      } else {
        let t = new Text(token);
        getTop(stack).childNodes.push(t);
        stack.push(t);
      }
    } else if (getTop(stack) instanceof Text) {
      stack.pop();
    }

    if (token instanceof StartTagToken) {
      let e = new Element(token);
      getTop(stack).childNodes.push(e);
      return stack.push(e);
    }
    if (token instanceof EndTagToken) {
      return stack.pop();
    }
  };

  this.getOutput = () => stack[0];
}

function getTop(stack) {
  return stack[stack.length - 1];
}

module.exports = {
  HTMLSyntaticalParser,
};

```

使用

```JavaScript
// test.js
const { HTMLSyntaticalParser } = require("./syntaxer");
const { HTMLLexicalParser } = require("./lexer");

const syntaxer = new HTMLSyntaticalParser();
const lexer = new HTMLLexicalParser(syntaxer);

//  html 文档
const testHTML = `<html maaa=a >
    <head>
        <title>cool</title>
    </head>
    <body>
        <img src="a" />
    </body>
</html>`;

for (let c of testHTML) {
  lexer.receiveInput(c);
}

console.log(JSON.stringify(syntaxer.getOutput(), null, 2));
```

这里我们最终得到了抽象语法树（虽然我们这里解析成了json）