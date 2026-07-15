# vue3使用

![[Pasted image 20240328181224.png]]

### vue3 创建

```js
import { createApp } from 'vue'
import App from './App.vue'
createApp(App).mount('#app')
```

### 根组件错误捕捉

```js
app.config.errorHandler = (err) => { /* 处理错误 */ }
```

### 生命周期钩子函数

- onBeforeMount：组件挂载前调用
- onMounted：组件挂载完成调用
- onBeforeUpdated：组件即将因为响应式状态变更而更新DOM树之前调用
- onUpdated：组件因为响应式状态变更而更新DOM树之后调用
- onBeforeUnmount：组件被卸载之前调用
- onUnmount：组件卸载之后调用

### ref

- 可以使用全部类型，需要通过value取值。value属性也是响应式数据，不管是解构替换赋值都不会改变其响应式

### reactive

- 会返回一个原始对象的Proxy，这个Proxy和原始对象是不相等的
- 对相同的原始对象进行调用reactive，会返回相同的Proxy。
- 对Proxy进行reactive 会返回其本身
- 局限性
  - 只能由于对象类型的数据响应
  - 不能替换整个对象，会丢失相应
  - 对解构赋值不友好，会丢失相应

```js
const state1 = reactive(data);
const state2 = reactive(data);
const state3 = reactive(state1);
console.log(state1 === data); //false 
console.log(state1 === state2); // true
console.log(state1 === state3); // true
```

### shallowReactive 和 shallowRef

- 浅层相应式数据

### Attribute绑定（属性绑定）

```vue
<template>
  <!-- id= "Hello World" -->
  <div v-bind:id="title"></div>
</template>

<script setup>
import { reactive, ref } from "vue";

let title = ref("Hello World");

function btnClick() {
  state++;
}
</script>
```

### v-model

- v-bind:value 和v-on:input 的语法糖
- input和change的区别：change失去焦点才触发，input是实时触发

### v-if 和 v-show

- v-if : 会移除元素
- v-show : 会隐藏元素， display:none; 切换使用v-show

### v-for

- key：唯一值，目的是更高效的更新虚拟DOM。在新旧节点对比时，可以根据key快速对比。

### computed

- 计算属性会自动跟踪其计算中所使用的其他**响应式**状态，并将其收集为自己的依赖。
- 计算结果会**缓存**，只有依赖发生改变时才会自动更新。
- 计算属性只会使用getter，**不要改变其他状态、在 getter 中做异步请求或者更改 DOM**，官方推荐不使用setter改变值

### defineAsyncComponent

- 异步加载组件

### 可以直接使用数组方法进行操作数组，响应式还存在

### watch (侦听器)

- 不能侦听响应式对象的属性值

```js
watch(监听的值/getter/数组, (新值, 旧值) => {});//深度监听、

//只监听inpValue，不会深度监听
watch(
  () => inpValue.value,
  (n, o) => {
    console.log(n, o);
  },
  {
    immediate: true,//立即执行
    deep: true,//深度监听
	 once: true //监听一次
  }
);
```

### watchEffect

- 立即执行，只会监听对应的属性，不会深度监听
- 在同步中会监听所有属性
- 在异步中只会侦听第一个await 中访问到的属性

```js
watchEffect(async () => {
  await inpValue.value;// 监听
  console.log("inpValue", inpValue.value); 
  await inpValue1.value;// 不监听
  console.log("inpValue1", inpValue1.value); 
});

watchEffect(() => {
  console.log("inpValue", inpValue.value);
  console.log("inpValue1", inpValue1.value);
});
```

### watch vs. watchEffect

`watch` 和 `watchEffect` 都能响应式地执行有副作用的回调。它们之间的主要区别是追踪响应式依赖的方式：

- `watch` 只追踪明确侦听的数据源。它不会追踪任何在回调中访问到的东西。另外，仅在数据源确实改变时才会触发回调。`watch` 会避免在发生副作用时追踪依赖，因此，我们能更加精确地控制回调函数的触发时机。
- `watchEffect`，则会在副作用发生期间追踪依赖。它会在同步执行过程中，自动追踪所有能访问到的响应式属性。这更方便，而且代码往往更简洁，但有时其响应性依赖关系会不那么明确。

### v-html

- 将有便签字符串渲染成html元素

### nextTick

- DOM的更新是异步操作
- 等待下一次 DOM 更新刷新，获取到最新的DOM数据
- promise的回调

```js
const count = ref(0);
const btn_red = ref(null);
function btnClick() {
  count.value++;
  console.log("count", count.value); //1
  console.log("button", btn_red.value); //<button>0</button>
  debugger;
  await nextTick()
  console.log("button", btn_red.value); //<button>1</button> 
}
```

### 事件修饰符（可以链式调用）

- .stop：事件停止传递
- .prevent：提交事件将不会重新加载页面
- .self：事件处理器不来自子元素，无事件委派
- .captrue：捕获模式，外部元素先触发再到内部元素
- .once：最多触发一次
- .passive：触摸事件监听器，用于改善移动端滚屏体验

### 按键修饰符（keyup）

- .enter：回车触发
- .page-down：pageDown按键触发

### 表单修饰符（v-model）

- .lazy：将input事件触发改为change事件触发
- .number：只能输入数字，type=number使用
- .trim：清除内容两端空格

### defineProps

```js
const props = defineProps(['name']);
console.log(props.name)
```

### defineEmits

```vue
// 父
<ChildView @child-handle="homeHandle" />

//子
<script setup>
const emit = defineEmits(["childHandle"]);
function btnClick() {
  emit("childHandle", "我是子组件传递的数据");
}
</script>
```

### defineExpose

- 将组件需要暴露的东西暴露出去，父组件可以调用

### 动态组件

```vue
<component :is="child_ref" />
<script setup>
	let child_ref = ref(markRaw(ChildView));
</script>
```

### 单向数据流

- props因父组件的更新而变化，自然地将新的状态向下流向子组件，不会逆向传递

### 透传

- 只有一个根元素的时候自动透传
- defineOptions({ inheritAttrs: false }) 禁用透传
- v-bind="$attrs" 指定透传，需要先禁止透传 或者 多根节点使用

### 依赖注入

- 解决Prop 逐级透传问题
- provide(注入名,值)
- inject(获取注入的名)

```js
//注入响应式数据
function getInpValue() {
  return inpValue.value;
}
provide("message", getInpValue);
```
