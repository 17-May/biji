# js使用ts提示

# 声明

![[Pasted image 20240409180455.png]]

```js
function add(options) {
  return options;
}

module.exports = {
  add,
};
```

```ts
export declare function add(options: { a: number; b: number }): object;
```

```json
//项目最外层的package.json定义即可
{
  "types": "index.d.ts"
}
```

# 使用

```js
const idx = require('../types/index')
console.log(idx.add());
```

# 注意

一个js对应一个.d.ts文件。文件名要相同
