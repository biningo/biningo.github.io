---
title: Js生态和一些概念
date: 2021-06-09
categories: [Js和前端]
tags: [js,Node]
draft: true
---

## ECMAScript

ES5 2009 

ES6 (2015发布，又叫ES2015），改动比较大，更新较多  很多浏览器不支持ES6以上版本的js，所以就需要借助工具将ES6代码转换为ES5

之后每年都会发布一个版本

ES7=ES2016

ES8=ES2017

........

​    

## CommonJS模块化标准和ES6模块化

`CommonJS`是一个项目，其目标是为Js建立模块化标准，NodeJS使用的是CommonJS规范来实现Js的模块化开发

CommonJS的特点如下:

- 全部一次性加载

```js
//a.js
module.exports = function () {
  console.log("hello world")
}

//b.js
var a = require('./a');

a();//"hello world"

//或者

//a2.js
exports.num = 1;
exports.obj = {xx: 2};

//b2.js
var a2 = require('./a2');

console.log(a2);//{ num: 1, obj: { xx: 2 } }
```

`ES6 Module` 是ES6中规定的模块体系，相比上面提到的规范， ES6 Module有更多的优势，有望成为浏览器和服务器通用的模块解决方案

浏览器中运行的前端框架比如React和Vue等都是使用ES6 Module来实现模块化开发的

ES6 Module特点如下:

- 按需加载

```bash
//a.js
var name = 'lin';
var age = 13;
var job = 'ninja';

export { name, age, job};

//b.js
import { name, age, job} from './a.js';

console.log(name, age, job);// lin 13 ninja

//或者

//a2.js
export default function () {
  console.log('default ');
}

//b2.js
import customName from './a2.js';
customName(); // 'default'
```

​    

## yarn

```bash
yarn list
yarn config list
yarn config get registory
yarn add lodash
yarn add lodash --dev
yarn remove lodash
yarn upgrade lodash
yarn install
yarn install --production
```

​     

## 参考

[聊聊什么是CommonJs和Es Module及它们的区别](https://juejin.cn/post/6938581764432461854)

[再次梳理AMD、CMD、CommonJS、ES6 Module的区别](https://juejin.cn/post/6844903983987834888)

