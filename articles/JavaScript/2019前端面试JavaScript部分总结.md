# 2019年前端面试之JavaScript部分总结

## 请说明ES5中 `Object.defineProperty` 和ES6中 `Proxy` 做数据响应的原理及其实现代码

`Object.defineProperty` 是 `ES5` 中的属性，传入一个描述对象即可描述一个对象的属性的特性。

我们用 `Object.defineProperty` 来实现对象读写时执行一些特定操作（比如发布属性更新的消息）。

```js
const data = {
  _a: 1,
  _b: 2
}

Object.keys(data).forEach(key => {
  const newKey = key.slice(1);
  Object.defineProperty(data, newKey, {
    get() {
      console.log(`read data's property: ${newKey}`);
      return data[key];
    },

    set(v) {
      console.log(`set data's property: ${newKey}, value: ${v}`);
      this[key] = v;
    },

    enumerable: true
  });

  Object.defineProperty(data, key, {
    enumerable: false
  })
});

data.b = 3;
data.b
// set data's property: b, value: 3
// read data's property: b
```

在这个例子中，`data` 作为一个消息发布者，在将 `data` 所有属性利用 `Object.defineProperty` 定义了他们的 `getter`, `setter` 后，每次更改 `data` 属性时，都将执行 `setter` 中定义的发布通知给`watcher` 的逻辑。

`Proxy` 中存在两个陷阱，一个是 `get` 陷阱，另一个是 `set` 陷阱。顾名思义，`get` 陷阱拦截读取属性的默认操作，`set` 陷阱拦截设置属性时的默认操作。另一方面，`Reflect` 同样具有对应行为的方法执行默认操作。

```js
const data = {
  a: 1,
  b: 2
}

const proxy = new Proxy(data, {
  get(target, key, receiver) {
    try {
      console.log(`read data's proerty: `, key);
      if (!(key in target)) {
        throw new Error(`属性不存在`);
      }

      return Reflect.get(target, key, receiver);
    } catch (error) {
      console.error(error);
    }
  },

  set(target, key, value, receiver) {
    try {
      console.log(`set data's property`, key);

      // 与get一样，不存在的属性不能执行set操作
      if (!(key in target)) {
        throw new Error(`属性不存在`);
      }

      // 这里可以操作发布通知给 watcher
      // ...

      // 继续执行赋值操作
      return Reflect.set(target, key, value, receiver);
    } catch (e) {
      console.errow(e);
    }
  }
})
```