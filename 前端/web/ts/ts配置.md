# ts配置

### 配置文件(tsconfig.ts)

- tsc --init：生成配置文件
- 有配置文件之后，编译不需要携带文件名。tsc 即可

### 第三方库

- @types/node：ts官方库类型库，包含js代码类型描述
- ts-node：将ts代码在内存中完成编译，同时完成运行。不会生成js文件，开发调试
  - npx ts-node 文件
- nodemon：监听文件是否修改，修改后重新运行文件
  - npx nodemon --watch src(监控的文件夹) -e ts(文件扩展名) --exec ts-node 文件
  - 可以将运行代码配置到package.json 中进行运行

```json
{
  //编译选项
  "compilerOptions": {
    "target": "es2015", //指定ECMAScript目标版本:ECMAScript是JavaScript的标准版本
    "module": "commonjs", //指定生成哪个模块系统代码
    "lib": ["ES2015"], //指定要包含在编译中的库文件 需要使用@types/node搭配使用配置node环境
    "outDir": "./dist", //指定输出目录
    "strictNullChecks": true, //启用严格的空值检查
    "removeComments": true, //移除注释
    "esModuleInterop": true, //启用es模块的互操作性
    "noEmitOnError": true, //在有错误时不生成输出文件
    "strictPropertyInitialization": true, // 严格检查类属性是否已初始化
    "declaration": true, //生成相应的.d.ts文件
    "experimentalDecorators": true, //启用实验性的装饰器
    "emitDecoratorMetadata": true, //在编译阶段将装饰器的元数据作为注释保留在输出文件中
    "moduleResolution": "node", //指定模块解析策略
    "paths": {
      "@/*": ["./src/*"] //设置路径映射
    },
    "skipLibCheck": true //跳过对声明文件的类型检查
  },
  //编译时的配置选项
  "include": ["./src/**/*.ts", "src/index.d.ts"], //指定要包含在编译中的文件或文件夹
  // "files": ["./src/index.ts"], //指定要包含在编译中的文件
  "exclude": ["node_modules"] //指定要排除在编译中的文件或文件夹
}

```
