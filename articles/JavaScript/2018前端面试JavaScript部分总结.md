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