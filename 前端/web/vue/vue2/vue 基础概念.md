# vue 基础概念

### SFC(Single File Component) 单文件组件

### 预编译

- 当`vue-cli`进行**打包**时，会直接把组件中的模板转换为`render`函数，这叫做模板预编译
- 运行时就不再需要编译模板了，提高了运行效率
- 打包结果中不再需要vue的编译代码，减少了打包体积

### 计算属性与普通函数（方法）

- 计算属性本质上是包含getter和setter方法
- 当获取计算属性时，实际上是在调用计算属性的getter方法。vue会收集计算属性的依赖，并缓存计算属性的返回结果。只有依赖发生改变后才会重新计算
- 方法没有缓存，每次调用都会重新执行

### scoped

- 会影响子组件的根元素样式，子组件其他元素不受影响

### css module

- 获取引入文件（css/less/scss）中的css类名，可以动态设置类名
- 命名：**文件名.module.类型后缀**

### 得到渲染的DOM

```js
/**
 * 获取某个组件渲染的Dom根元素
 * @param {Component} comp 组件
 * @param {Object} props 配置
 * @returns dom
 */
function getComponentRootDom(comp, props) {
  const vm = new Vue({
    render: (h) => h(comp, { props }),
  });
  vm.$mount();
  return vm.$el;
}
```
