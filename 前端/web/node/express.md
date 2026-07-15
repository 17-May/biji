# express

### 启动使用nodemon

```js
const express = require('express');
const app = express();
//路由
app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(3000)
```

### 中间件

- static:静态资源
- router:路由
