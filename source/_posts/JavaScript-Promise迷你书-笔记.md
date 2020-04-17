---
title: JavaScript Promise迷你书--笔记
date: 2017-12-14 09:49:08
category: js
tags: [promise, 异步, doc]
---
    
##### 1. `.then .catch` 更像是 回调函数罢了
```
var promise = new Promise(function(resolve, reject) {
    // 异步处理
    let getDataSuccess = false
    if (getDataSuccess) {
      resolve('response')
    } else {
      reject('error')
    }
})

promise.then(undefined, (error) => {
  console.log(error)
})
```

##### 2. 其实 `.catch` 只是 `promise.then(undefined, onRejected)` 的别名而已
到目前为止我们已经学习了如何通过 .then 和 .catch 来注册回调函数，这些回调函数会在promise对象变为 FulFilled 或 Rejected 状态之后被调用。

##### 3. Promise.resolve 方法的话，可以认为它的作用就是将传递给它的参数填充（Fulfilled）到promise对象后并返回这个promise对象

##### 4. Promise.reject(error)是和 Promise.resolve(value) 类似的静态方法，是 new Promise() 方法的快捷方式。
```
比如 Promise.reject(new Error("出错了")) 就是下面代码的语法糖形式。

new Promise(function(resolve,reject){
    reject(new Error("出错了"));
});
```

##### 5. new 一个promise的时候，其内部的一些程序就已经执行了。就像是在 constructor当中直接执行某些代码
```
new Promise((resolve, reject) => {
  console.log('inner')
  resolve('hehe')
}).then((res) => {
  console.log(res)
})
console.log('outer')

inner promise // 1
outer promise // 2
42            // 3
```

##### 6. Promise在规范上规定 Promise只能使用异步调用方式 。

##### 7. Promise#then 不仅仅是注册一个回调函数那么简单，它还会将回调函数的返回值进行变换，创建并返回一个promise对象

##### 8. 这里我们再说一遍，实际上 Promise#catch 只是 promise.then(undefined, onRejected); 方法的一个别名而已。 也就是说，这个方法用来注册当promise对象状态变为Rejected时的回调函数。

##### 9. return的值会由 Promise.resolve(return的返回值); 进行相应的包装处理，因此不管回调函数中会返回一个什么样的值，最终 then 的结果都是返回一个新创建的promise对象。
```
var bPromise = new Promise(function (resolve) {
    resolve(100);
});
bPromise.then(function (value) {
    return value * 2;  // 默认使用的是 Promise.resolve(), 除非 显示的调用 Promise.catch()， 否则 一直 then下去
}).then(function (value) {
    return value * 2;
}).then(function (value) {
    console.log("2: " + value); // => 100 * 2 * 2
});
```


##### 第二遍看，重要语句概念摘抄
1. promise的功能是可以将复杂的异步处理轻松地进行模式化， 这也可以说得上是使用promise的理由之一
2. new生成的promise对象为了设置其值在 resolve(成功) / reject(失败)时调用的回调函数 可以使用promise.then() 实例方法。
promise.then(onFulfilled, onRejected)
3. promise.then 成功和失败时都可以使用。 另外在只想对异常进行处理时可以采用 promise.then(undefined, onRejected) 这种方式，只指定reject时的回调函数即可。 不过这种情况下 promise.catch(onRejected) 应该是个更好的选择。
4. 用new Promise 实例化的promise对象有以下三个状态。
```
"has-resolution" - Fulfilled
resolve(成功)时。此时会调用 onFulfilled

"has-rejection" - Rejected
reject(失败)时。此时会调用 onRejected

"unresolved" - Pending
既不是resolve也不是reject的状态。也就是promise对象刚被创建后的初始化状态等
```

5. 一般情况下我们都会使用 new Promise() 来创建promise对象，但是除此之外我们也可以使用其他方法。 在这里，我们将会学习如何使用 Promise.resolve 和 Promise.reject这两个方法。  
静态方法Promise.resolve(value) 可以认为是 new Promise() 方法的快捷方式。

比如 `Promise.resolve(42)`; 可以认为是以下代码的语法糖。
```
new Promise(function(resolve){
    resolve(42);
});
```

### 参考资料
[JavaScript Promise迷你书（中文版）](http://liubin.org/promises-book/)
