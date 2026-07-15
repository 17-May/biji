# Redux

##### 什么是Redux

redux是一个使用叫做“actions”的事件去管理和更新应用状态的模式和工具库。核心就是订阅发布模型，订阅状态修改的回调函数，状态更新。

##### 我为什么要用Redux

Redux提供的模式和工具使你更容易理解应用程序中的状态何时、何地、为什么以及如何更新，以及当这些更改发生时你的应用程序逻辑将如何表现

##### 我什么时候应该用Redux

- 需要进行状态共享的时候
- 中大型项目，多人协同开发

# api

- getStore：获取数据状态
- subscribe：订阅数据
- dispatch：发布

### 流程

1. 发起dispatch->给reducer传入actions指令->reducer判断actions指令执行对应的计算->生成新的state->更新UI
2. 只要dispatch调用，就会执行subscribe，无论数据有没有变化。实际上数据已经发生变化，只是两个值相同
