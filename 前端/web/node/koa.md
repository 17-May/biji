# koa

### ctx

内置了node原生的request和response方法

### next

将处理的控制权转交给下一个的中间件
next之前是request,之后是response

##### 洋葱模型

从外到内，再从内到外。中间中断了则不会往下运行，只会执行到该层。
![[Pasted image 20240413133911.png]]

```js
app.use(async (ctx, next) => {
  console.log("request start");
  await next();
  console.log("response end");
});
app.use(async (ctx, next) => {
  console.log("111");
  await next();
  console.log("111 end");
});
app.use(async (ctx, next) => {
  console.log("222");
  await next();
  console.log("222 end");
});

app.use(async (ctx, next) => {
  console.log("333");
  await next();
  console.log("333 end");
});

request start
111
222
333
333 end
222 end
111 end
response end
```

### 中间件

- koa-body ：获取post参数，文件参数
- koa-bodyparser : 获取post参数，不能获取文件参数
- koa-multer ： 获取文件参数
- koa-router : 路由
