# 执行栈和执行上下文（ExecutionContent）

有哪些JavaScript代码时，当代码执行进入一个环境时，就会为该环境创建一个执行上下文，它会在你运行代码前做一些工作，如确定作用域，创建全局、局部变量对象等

#### JavaScript中执行环境

1. 全局环境
2. 函数环境
3. eval环境（不推荐使用）

##### 对应的上下文类型

1. 全局执行上下文
2. 函数执行上下文
3. eval函数执行上下文

### 上下文

- 实际上就是为该段代码在栈中创建一个运行函数的环境，逐步将运行到的函数放到栈内，在逐步执行
- 栈：先进后出，后进先出

#### 堆栈溢出

- 执行上下文可存在多个，没有明确的数量限制，如果超出栈分配空间会照成堆栈溢出。常见于递归、死循环

### 执行上下文生命周期

1. 创建阶段：函数被调用时，进入函数环境，创建一个执行上下文
   1. 创建变量对象（vo:variable object）
      1. 确定函数的形参（赋值）
      2. 函数环境会初始化创建Arguments对象（赋值）
      3. 确定普通字面量形式的函数（赋值）
      4. 变量声明（为赋值）
   2. 确定this的指向
   3. 确定作用域名
2. 执行阶段：执行函数中的代码
   1. 变量对象赋值
   2. 调用函数
   3. 顺序执行其他代码

```javascript
const foo = function (i) {
  var a = "hello";
  var b = function privateB() {};
  var c = function () {};
};
foo(10);

// 创建全局执行上下文
fooExecutionContent = {
  v0: {
    i: 10,
    arguments: {
      0: 10,
      length: 1,
      c: "function () {}",
      a: undefined,
      b: undefined,
    },
  },
  this: window,
  scopeChain: {},
};
// 执行foo函数
fooExecutionContent.vo = {
  a: "hello",
  b: "function privateB() {}",
  c: "function () {}",
};

```
