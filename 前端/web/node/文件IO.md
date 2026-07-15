# 文件IO

I/O: input output

# fs模块

- 读取文件:fs.readFile
- 写入文件:fs.writeFile
- 获取文件信息:fs.stat
- 获取目录中文件或子目录:fs.readdir
- 创建目录:fs.mkdir
- 判断文件是否存在：fs.access

### 新增了promise

`fs.promise.xx`
方法和原来的一样，只是变成promise方法

# 流

数据的流动，数据从一个地方缓缓流动到另一个地方

- 可读流（Readable）：数据从源头流向内存
- 可写流（Writable）：数据从内存流向源头
- 双工流（Duplex）：数据从源头流向内存，又可以从内存流向源头

### 可读流

`fs.createReadStream(path [,options])`

- path：路径
- options：配置对象
  - encoding：编码
  - start：起始字节
  - end：结束字节
  - highWaterMark：每次读取数量
    - encoding有值，表示字符数
    - encoding无值，表示字节数
  - autoClose：读取完成是否关闭
- 返回：Readable的子类ReadStrem。
  - rs.on(事件名，处理函数)
  - rs.pause()：暂停读取
  - rs.resume()：恢复读取

### 可写流

`fs.createWriteStream(path [,options])`

- path：路径
- options：配置对象
  - flags：操作文件的方式
    - a：不覆盖
    - w：覆盖
  - encoding：编码
  - start：起始字节
  - highWaterMark：每次写入字符数
  - autoClose：读取完成是否关闭
- 返回：Writable的子类WriteStream。
  - ws.on(事件名，处理函数)
  - ws.wirte(data)
    - data可以是字符串或Buffer
    - 返回一个boolean值（与highWaterMark相关）
      - true：写入通道没有被填满，接下来的数据可以直接写入，不需要排队
      - false：写入通道已被填满，接下来的数据将进入写入队列（产生背压）
    - 写入队列清空时，会触发drain事件
  - ws.end()：恢复读取

### 管道-pipe（解决背压问题）

`rs.pipe(ws)`
相当于这个代码

```js
  rs.on("data", (chunk) => {
    const flag = ws.write(chunk);
    if (!flag) {
      rs.pause();
    }
  });
  ws.on("drain", () => {
    rs.resume();
  });
```

# 获取文件

```js
const fs = require("fs");
const path = require("path");
class File {
  constructor(filename, name, ext, isFile, size, createdTime, modifiedTime) {
    this.filename = filename;
    this.name = name;
    this.ext = ext;
    this.isFile = isFile;
    this.size = size;
    this.createdTime = createdTime;
    this.modifiedTime = modifiedTime;
  }
  static async getFile(fileName) {
    const stats = await fs.promises.stat(fileName);
    const name = path.basename(fileName);
    const ext = path.extname(fileName);
    const isFile = stats.isFile();
    const size = stats.size;
    const createdTime = new Date(stats.birthtime);
    const modifiedTime = new Date(stats.mtime);
    return new File(
      fileName,
      name,
      ext,
      isFile,
      size,
      createdTime,
      modifiedTime
    );
  }
  async getContent(isBuffer = false) {
    if (this.isFile) {
      if (isBuffer) {
        return await fs.promises.readFile(this.filename);
      }
      return await fs.promises.readFile(this.filename, "utf8");
    }
    return null;
  }
  async getChiledren(recursive = false) {
    if (this.isFile) {
      return [];
    }
    let chiledren = await fs.promises.readdir(this.filename);
    chiledren = chiledren.map((filename) => {
      const chileFileUrl = path.join(this.filename, filename);
      return File.getFile(chileFileUrl);
    });
    const childrenFiles = await Promise.all(chiledren);
    if (recursive) {
      for (const file of childrenFiles) {
        if (!file.isFile) {
          file.chiledren = await file.getChiledren(recursive);
        }
      }
    }
    return childrenFiles;
  }
}
async function readdir(dirname) {
  const files = await File.getFile(dirname);
  return files.getChiledren(true);
}
async function test() {
  const fileUrl = path.resolve(__dirname, "test");
  const file = await readdir(fileUrl);
  console.log(JSON.stringify(file, null, 2));
}
test();

```

# 复制文件

```js
async function readFile() {
  const data = await fs.promises.readFile(filePath, "utf-8");
  await fs.promises.writeFile(filePath2, data);
}

function writeFile() {
  const rs = fs.createReadStream(filePath, {
    encoding: "utf-8",
  });
  const ws = fs.createWriteStream(filePath2);
  rs.on("data", (chunk) => {
    const flag = ws.write(chunk);
    if (!flag) {
      rs.pause();
    }
  });
  ws.on("drain", () => {
    rs.resume();
  });
  // rs.pipe(ws);
  rs.on("close", () => {
    ws.end();
  });
}
```
