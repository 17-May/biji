# Promise和asnyc await

# Promise

- promise是一套专门处理异步场景的规范，有效避免回调地狱。
- promise状态确定后，不会改变

### Promise A+规范

1. 所有的异步场景，都可以看作一个任务，每一个异步任务，在JS中应该表现为一个对象，该对象称之为Promise对象，也叫任务对象
2. 每个任务对象，都应该有两个阶段、三个状态
   ![[Pasted image 20240315065219.png]]
3. 挂起->完成，称之为 **resolve** ; 挂起->失败，称之为**reject** 。
   ![[Pasted image 20240315065752.png]]
4. 可以针对任务进行后续处理，针对完成状态的后续处理称之为onFukfilled,针对失败的后续处理称之为onRejected
   ![[Pasted image 20240315070217.png]]

##### 使用方法

```js
const promise = new Promise((resolve, reject) => {});
promise.then(
  (data) => {
    console.log("resolved");
  },
  (reason) => {
    console.log("rejected");
  }
);

```

##### catch方法

.catch(onRejected) = .then(null,onRejected)

### 链式调用

![[Pasted image 20240315085055.png]]

1. then方法必定会返回一个新的promise
   - 可理解为 后续处理也是一个任务
2. 新任务的状态取决于后续处理
   - 若没有相关的后续处理，新任务的状态和前任务一致，数据为前任务的数据
   - 若有后续处理但未执行，新任务挂起
   - 若后续处理执行了，则根据后续处理的情况确定新任务的状态
     - 后续处理执行无错，新任务的状态为完成，数据为后续处理的返回值
     - 后续处理执行有错，新任务的状态为失败，数据为异常对象
     - 后续执行后返回的是一个任务对象，新任务的状态和数据与该任务对象一致

```js
//第一种
const promise1 = new Promise((resolve, reject) => {
  resolve("Success");
});
const promise2 = promise1.catch((res) => {});
setTimeout(() => {
  console.log(promise2); //Promise { 'Success' }
}, 0);
//第二种
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Success");
  }, 1000);
});
const promise2 = promise1.then((res) => {});
setTimeout(() => {
  console.log(promise2); //Promise { <pending> }
}, 0);

```

### Promise静态方法

|方法名|含义|
| ----------------------------| --------------------------------------------------------|
|Promise.resolve(data)|直接返回一个完成状态的任务|
|Promise.reject(reason)|直接返回一个拒绝状态的任务|
|Promise.all(任务数组)|返回一个任务，任务数组全部成功则成功，任何一个失败则失败|
|Promise.any(任务数组)|返回一个任务，任务数组成功一个则成功，任务全部失败则失败|
|Promise.allSettled(任务数组)|返回一个任务，任务数组全部已决则成功，该任务不会失败|
|Promise.race(任务数组)|返回一个任务，任务数组任一已决则已决，状态和其一致|

# async

- async 关键字用于修饰函数，被修饰的函数一定返回Promise。**es7（2016）推出**

# await

- await 关键字表示等待某个Promise完成，必须用于async函数中。如果不是Promise则会转为Promise
- await返回成功结果，失败时会抛出异常。使用try-catch捕获异常
