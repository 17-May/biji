# JWT（JSON Web Token）

# 没有JWT的世界

- 浏览器的发送信息可以被篡改或者伪造
- 服务器无法验证信息是否正确
  ![[007f9fb29aaadb69d00777a91741e6f.jpg]]

# 签名

- 在服务器返回给浏览器的信息加签名，可以验证浏览器后面发送给服务器的信息是正确的
  ![[d233bcf38ff177cc3dacd8377d46f9d.jpg]]

# JWT含义

- JWT是一个JSON格式的网络传输令牌
- JWT格式：header + . + payload + . + signature
  ![[Pasted image 20240219162502.png]]

```JS
const crypot = require("crypto");

//创建签名token

function signToken(msg, key) {

  return crypot.createHmac("sha256", key).update(msg).digest("hex");

}

//创建base64  

function createBase64(str) {

  str = JSON.stringify(str);

  return Buffer.from(str, "utf-8").toString("base64");

}

//解码base64

function decodeBase64(str) {

  return Buffer.from(str, "base64").toString("utf-8");

}

const header = { alt: "HS256", typ: "JWT" };//签名和类型

const payload = { name: "zhangsan", age: 18 };//用户数据

const headerBase64 = createBase64(header);

const payloadBase64 = createBase64(payload);

const signature = signToken(headerBase64 + "." + payloadBase64, "123");

const signatureBase64 = createBase64(signature);

const JWT = headerBase64 + "." + payloadBase64 + "." + signatureBase64;

console.log(JWT);
```
