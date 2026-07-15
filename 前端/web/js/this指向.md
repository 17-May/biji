# this指向

- 函数中使用this,完全取决于函数如何被被调用

### 规律

- 非显示或者隐式调用函数。严格模式下，函数内的this会被绑定到undefined。非严格模式下，window/global。
- 使用new 方法调用构造函数，this会被绑定到新构建对象上
- call/apply/bind 显示调用函数，this会被绑定到指定参数的对象上
- 上下文对象调用，this绑定到该对象上
- 箭头函数，this指向由外层作用域决定

# call、apply、bind

### call

- **fn.call(对象，传参，传参...)**
- this指向传入的参数的作用域，立即执行函数
- 参数为null、undefined时，指向全局

```js
var obj = {}
function test() {
  return this
}
console.log(test.call(obj)) // obj;
console.log(test.call()) // window;

Object.prototype.hasOwnProperty.call(obj, 'toString') // 获取对象的原型链上的原生方法
```

### apply

- **fn.apply(对象，数组)**
- this指向传入的参数的作用域，立即执行函数
- 参数为null、undefined时，指向全局

```js
var obj = {};
function test(a, b) {
  console.log(a, b);
  return this;
}
console.log(test.apply(obj, [1, 2])); // obj;
console.log(test.apply()) // window;
```

### bind

- **fn.bind(对象，参数，参数...)**
- 将函数内的this绑定到对象，返回新函数

```js
var obj = {};
function test(a, b) {
  console.log(a, b);
  return this;
}
const test1 = test.bind(obj, 1, 2);
console.log(test1() === obj); // true
```
