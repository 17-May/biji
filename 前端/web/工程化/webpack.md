# webpack

# 打包流程

输入->模块处理->后处理->输出

- 输入：从文件系统读取代码文件
- 模块处理：调用Loader转译Module内容，并将结果转换为AST，从中分析出模块依赖关系，进一步递归调用模块处理过程，直到所有依赖文件处理完毕
- 后处理：所有模块递归处理完毕后开始执行后处理，包括模块合并、注入运行时、产物优化等，最终输出Chunk集合
- 输出：将Chunk写出到外部文件系统
  从打包流程角度，webpack配置大体分为两类：
- 流程类：作用于打包流程某个或若干个环节，直接影响编译打包效果的配置项
- 工具类：打包主流程之外，提供更多工程化工具的配置项

### 主流程

- 输入输出
  - **entry**:项目入口
  - **context**:项目执行上下文路径
  - **output**:输出路径、名称
- 模块处理
  - **resolve**:配置模块路径解析规则
  - **module**:配置模块加载规则, loader
  - **externals**:声明外部资源，webpack会忽略这部分资源
- 后处理
  - **optimization**:控制打包体积、代码混淆、代码压缩
  - **target**:配置运行环境（web、node、electron）
  - **mode**:声明环境的短语（development、production）

### 工具类

- 开发效率类
  - **watch**:用于配置持续监听文件变化，持续构建
  - **devtool**:用于配置产物Sourcemap生成规则
  - **devServer**:用于配置与HMR强相关的开发服务器功能
- 性能优化类
  - **cache**:webpack5之后，该项用于控制如何缓存编译过程信息与编译结果
  - **performance**:用于配置当产物大小超过阈值时，如何通知开发者
- 日志类
  - **stats**:用于精确地工资编译过程的日志内容，在做比较细致的性能调试时非常有用
  - **infrastructureLogging**:用于控制日志输出方式，例如可以通过该配置将日志输出到磁盘文件

### webpack.config.js

```js
const path = require("path");
// 自动生成html文件
const HtmlWebpackPlugin = require("html-webpack-plugin");
// 自动生成service-worker.js文件，提升PWA的离线体验
const WorkboxWebpackPlugin = require("workbox-webpack-plugin");
// 将css相关的js代码提取到单独的css文件中,一般在生产环境下使用。style-loader一般在开发环境下使用。
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
// 自动清理dist目录
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const loader = require("css-loader");

const isProduction = process.env.NODE_ENV == "production";

const config = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
  },
  devServer: {
    open: false,
    host: "localhost",
    port: 3000,
    compress: true, //压缩传输数据
    // hot: true, //热更新
  },
  //开发环境下使用development，生产环境下使用production。production模式下会自动压缩代码，去除console.log等调试信息。
  // production模式下，能看到原始的内容，便于调试
  // mode: "production",
  // devtool: "source-map", //生成source-map文件，便于调试
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      template: "index.html",
    }),
    new MiniCssExtractPlugin({
      filename: "[name].[contenthash:8].css",
      chunkFilename: "[name].[contenthash:8].chunk.css",
    }),
  ],
  module: {
    rules: [
      {
        test: /\.less$/i, //匹配css文件
        exclude: /node_modules/, //排除node_modules目录
        use: [
          //   "style-loader",
          MiniCssExtractPlugin.loader,
          "css-loader",
          //"less-loader",//将less文件编译成css文件
          "postcss-loader", //将css文件进行自动化处理，编译平台
        ], //执行顺序从右向左，css-loader先执行，将css文件转换成commonjs模块，再交给style-loader处理
      },
      {
        test: /\.js$/i,
        exclude: /node_modules/,
        use: { loader: "babel-loader" }, //将es6语法转换成es5语法
      },
    ],
  },
};

module.exports = () => {
  if (isProduction) {
    config.mode = "production";
    config.plugins.push(new WorkboxWebpackPlugin.GenerateSW());
  } else {
    config.mode = "development";
  }
  return config;
};

```

### postcss.config.js

```js
module.exports = {
  plugins: [
    // require("autoprefixer"), //自动添加浏览器前缀
    // require("cssnano"), //压缩css文件
    require("postcss-cssnext"),//使用cssnext插件，可以让css代码更加简洁，并且可以自动添加浏览器前缀，并且可以利用css变量
  ],
};

```

### babel.config.json

```js
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": "current"
        }
      }
    ]
  ]
}

```
