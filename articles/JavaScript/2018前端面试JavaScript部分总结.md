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

## 如何数组去重？写出所有可能的实现代码

```js
// forEach
let arr = ['1', '2', '3', '1', 'a', 'b', 'b']
const unique = arr => {
    let obj = {}
    arr.forEach(value => {
        obj[value] = 0
    })
    return Object.keys(obj)
}

// filter
let arr = ['1', '2', '3', '1', 'a', 'b', 'b']
const unique = arr => {
    return arr.filter((ele, index, array) => {
        return index === array.indexOf(ele)
    })
}

// set
let arr = ['1', '2', '3', '1', 'a', 'b', 'b']
const unique = arr => {
    return [...new Set(arr)]
}
```

## 阐述浅拷贝与深拷贝的区别，并手写代码实现深拷贝。

在JavaScript中，数据类型分为基本数据类型（`String`、`Number`、`Boolean`、`Undefined`、`Null`和`Symbol`）和引用数据类型（`Object`、`Array`和`Function`）。

基本类型的数据存储在栈空间（stack）中，而引用数据类型存储在堆空间中。引用数据类型在栈中存储了指针，该指针指向了该数据实体在堆空间的实际地址，解释器会依据这个地址来获取数据实体。

深浅拷贝都只是针对 `Object` 或 `Array` 这些引用类型的。浅拷贝只复制指向某个对象的指针，而非复制对象本身，新旧对象只是共享一块内存。而深拷贝则是创建另外一个一模一样的对象，它们不共享内存，修改哪一个都不会影响到另一个。

现在的问题是，一个对象可能非常复杂，因为其元素的值又是一个引用类型，比如数组或对象，这时，浅拷贝可以复制其第一层的数据，而元素值是引用类型的，则复制的是引用指针，所以，这时我们就需要深拷贝，来复制目标对象中所有层级中的值，无论是基本类型或引用类型。

浅拷贝可以使用 `Object.assign` 方法来实现：

```js
var obj = { a: { a: 'kaindy', b: 100 } };
var copyObj = Object.assign({}, obj);
copyObj.a.a = 'liuzhen';
obj.a.a;  // liuzhen
```

如果目标只有一层，则使用 `Object.assign` 就是深拷贝。

下面来看下实现深拷贝的方法，首先，我们可以使用 `JSON.parse(JSON.stringify)` 来实现

```js
let arr = [1, 3, { username: 'kaindy' }];
let copyArr = JSON.parse(JSON.stringify(arr));
copyArr[2].username = 'liuzhen';
arr[2].username;    // kaindy
```

但这方法不能解决函数拷贝的问题，即如果元素值是函数，则拷贝会失败。所以，我们只能使用递归的方式来手动写深拷贝代码：

```js
// 首先定义一个推断数据类型的函数
function checkType(target) {
  return Object.prototype.toString.call(target).slice(8, -1);
}

// 实现深度克隆（对象/数组）
function deepClone(target) {
  // 判断目标类型
  let result, targetType = checkType(target);
  if (targetType === 'Object') {
    result = {};
  } else if (targetType === 'Array') {
    result = [];
  } else {
    // 基本数据类型，直接返回目标
    return target;
  }

  // 遍历目标数据
  for (let i in target) {
    // 获取每一项
    let value = target[i];
    // 判断每一项是否为引用类型
    if (checkType(value) === 'Object' || checkType(value) === 'Array') {
      result[i] = deepClone(value);     // 执行递归
    } else {
      result[i] = value;
    }
  }

  // 返回结果
  return result;
}
```

## 使用正则实现一个 `trim()` 函数

```js
function trim(str) {
  let reg = /^\s+|\s+$/g;
  return str.replace(reg, "");
}
```

## 请解释原型与原型链

原型是 `ECMAScript` 实现继承的过程中产生的一个概念，而原型链则是 `ECMAScript` 实现继承的一种机制。

JavaScript中的每个对象都有一个内置的 `__proto__` 属性指向创建它的构造函数的 `prototype` （原型）属性，而 `prototype` 则是函数才具有的属性。

在JavaScript中，函数也是对象。普通的对象，比如使用字面量方法或构造函数方法创建的对象，都具有 `__proto__` 属性，而函数对象才拥有 `prototype` 属性（原型属性）

```js
// 普通对象，自创建起就拥有 __proto__ 属性， 并指向 Object
var o1 = {};
var o2 = new Object();

// 函数对象
function F1() {};
var F2 = function() {};
var F3 = new Function();
```

`prototype` 属性的值是一个对象，它只有一个属性： `constructor`。 `constructor` 是一个公有且不可枚举的属性。

`__proto__` 是每个对象都有的隐式原型属性，它指向创建该对象的构造函数的原型，即 `[[prototype]]`，但 `[[prototype]]` 是一个内部属性且不允许访问，所以我们可以使用 `__proto__` 来访问。

要在JavaScript中实现继承，我们就可以通过 `__proto__` 属性将对象和原型联系起来组成原型链，就可以通过其访问不属于自己的属性了。

当我们使用 `new` 操作符时，生成的实例对象就拥有了 `__proto__` 属性，所以可以说在 `new` 的过程中，新对象被添加了 `__proto__` 并且连接到构造函数的原型上了。

`new` 的过程如下：

1、 新生成了一个对象
2、 链接到原型
3、 绑定 `this`
4、 返回新对象

我们可以来自己实现一个 `new` 函数：

```js
function create() {
  let obj = new Object();   // 创建一个空对象
  let Con = [].shift.call(arguments);   // 获取构造函数
  obj.__proto__ = Con.prototype;    // 链接到原型
  let result = Con.apply(obj, arguments);   // 绑定this并执行构造函数
  
  // 确保 new 出来的是一个对象
  return typeof result === 'object' ? result : obj;
}
```