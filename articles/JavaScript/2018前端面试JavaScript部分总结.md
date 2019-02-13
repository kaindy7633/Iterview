# 2018年前端面试之JavaScript部分总结

## JS数据类型

- `Number`
- `String`
- `Boolean`
- `Undefined`
- `Null`
- `Symbol` （ES6新增，全局唯一）
- `Object` （包括 `object`、`array` 和 `function`）

## 简述如何正确判断变量是否为 `Array` 类型，说出你知道的所有方法。

- 使用 `Array` 对象上的 `isArray` 方法可以直接判断参数是否为数组

  ```js
  Array.isArray([]);   // true
  Array.isArray(100);   // false
  ```

- 使用 `Array` 原型上的 `isPrototypeOf` 方法判断参数是否为数组

  ```js
  Array.prototype.isPrototypeOf([]);    // true
  Array.prototype.isPrototypeOf({});    // false
  ```

- 使用 `Object` 原型上的 `toString` 方法，通过 `apply` 方法作用于参数上，会返回当前参数的类型字符串

  ```js
  Object.prototype.toString.apply([]);    // [object Array]
  Object.prototype.toString.apply({});    // [object Object]
  ```

- 通过 `instanceof` 方法判断

  ```js
  [] instanceof Array;    // true
  ```

## 解释什么是闭包及其作用，并说出下列代码的输出什么？如何使用闭包得到正确的结果。

MDN上对闭包的解释是能够访问自由变量的函数，而自由变量指的是咋函数中使用，既不是函数参数也不是函数局部变量的变量。所以由此得出，闭包 = 函数 + 函数能访问的自由变量。

从技术角度上讲，所有的JavaScript函数都是闭包，而从实践角度上讲， 闭包指的是即使创建它的上下文已销毁而它仍然存在且引用了自由变量的函数。

闭包可以用来创建块级作用域（ES6中使用`let`和`const`），且可以创建私有变量

```js
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = function () {
    console.log(i);
  };
}

data[0]();    // 3
data[1]();    // 3
data[2]();    // 3

// 改造如下：
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = (function (i) {
        return function(){
            console.log(i);
        }
  })(i);
}

data[0]();    // 0
data[1]();    // 1
data[2]();    // 2
```

## 什么是立即执行函数IIFE，它有什么作用？

立即执行函数即无需显式调用就会被执行的函数，它有两种写法：

```js
// 1
(function(x) {
  console.log(x);
})(10);

// 2
(function(x) {
  console.log(x);
}(10))
```

立即执行函数可创建不污染全局的命名空间。