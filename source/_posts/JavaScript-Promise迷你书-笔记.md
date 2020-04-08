---
title: JavaScript Promise迷你书--笔记
date: 2017-12-14 09:49:08
category: js
tags: [promise, 异步, doc]
---


### 一、 乞丐版promise
仅实现 非常乞丐版的 promise，仅有个形式
```
enum PromiseStatus {
  '',
  Pendding,
  Fulfilled,
  Rejected
}

class MyPromise {
  private status: PromiseStatus;
  private fulfilledCallBacks = [];
  private rejectedCallBack = [];

  constructor(executor) {
    this.status = PromiseStatus.Pendding;
    executor(this.resolve.bind(this), this.reject.bind(this));
  }

  private resolve(value?: any) {
    if (this.status !== PromiseStatus.Pendding) {
      return;
    }
    this.status = PromiseStatus.Fulfilled;
    setTimeout(() => {
      this.flushQueue(this.fulfilledCallBacks, value);
    });
  }

  private reject(value?: any) {
    if (this.status !== PromiseStatus.Pendding) {
      return;
    }
    this.status = PromiseStatus.Rejected;
    setTimeout(() => {
      this.flushQueue(this.rejectedCallBack, value);
    });
  }

  public then(fulfiledCb?: any, rejectedCb?: any) {
    if (fulfiledCb) {
      this.fulfilledCallBacks.push(fulfiledCb);
    }
    if (rejectedCb) {
      this.rejectedCallBack.push(rejectedCb);
    }
    return this;
  }

  public catch(rejectedCb?: any) {
    this.then(undefined, rejectedCb);
    return this;
  }

  private flushQueue(queue: any[], value?: any) {
    let _value = value;
    while (queue.length) {
      const cb = queue.shift();
      if (cb) {
        _value = cb(_value);
      }
    }
  }
}

let a = new MyPromise((resolve, reject) => {
  resolve('feaw');
})
  .then(res => {
    return res
  })
  .then(res => {
    console.log('res: ', res);
  })
  .catch(err => {
    console.log('err: ', err);
  });

console.log(a);
```

### 二、 实现 promise 的 核心方法 then 的部分，仅做收集 回调函数的作用
1. 回调函数所执行的语句为异步时，promise.state 则处于 pendding 状态，在 pendding 状态时，promise.callbacks 则可趁此时机手机 then 方法中的 回调函数
2. promise.then 方法另外一个核心点时，其每次都返回一个全新的 promise，起作用有两点
    * 链式调用  
    * 每个 `then` 方法后如果还是在跟着一个 `then` 方法，则将后者的回调函数收集到前面的 promise.callbacks 当中去。  
    即，每个 then 方法返回的 `promise` 都去做收集跟随其后面 `then` 方法中的回调函数

```
enum PromiseStatus {
  '',
  Pendding,
  Fulfilled,
  Rejected
}
class MyPromise {
  private state: PromiseStatus;
  private fulfilledCallBacks = [];
  private value: any = null;
  constructor(executor) {
    this.state = PromiseStatus.Pendding;
    executor(this.resolve.bind(this));
  }
  private resolve(value?: any) {
    this.state = PromiseStatus.Fulfilled;
    this.value = value;

    this.fulfilledCallBacks.forEach(callback => {
      const ret = callback.cb(this.value);
      callback.resolve(ret);
    });
  }
  public then(fulfiledCb?: any, rejectedCb?: any) {
    return new MyPromise((resolve, reject) => {
      if (this.state === PromiseStatus.Pendding) {
        // 将 then 方法返回的 promise 的resolve 收集到 上一个调用者的 cb 队列当中
        // 这是实现 值穿透（值在几个promise间传递）的关键
        this.fulfilledCallBacks.push({
          resolve: resolve,
          cb: fulfiledCb
        });
      }
    });
  }
}

const mockAjax = (url, s, callback) => {
  console.log('start async async')
  setTimeout(() => {
    callback(url + '异步请求耗时' + s + '秒');
    console.log('end async task')
  }, 1000 * s);
};

// Demo1 回调函数所执行的语句为异步的
// 这种情况下，then 的回调函数 会放入到 promise.callbacks 回调队列当中
// 等待 异步函数执行玩 resolve 之后 去执行
new MyPromise(resolve => {
  mockAjax('getUserId', 2, function(result) {
    resolve(result);
  });
})
  .then(result => {
    return `prefix: ${result}`;
  })
  .then(res => {
    console.log(res);
  });
```

### 三、 实现 promise 的 核心方法 then 的部分，增加直接执行 回调函数的机制
1. 回调函数所执行的语句为同步时，`promise` 直接 `resolve` 了，这样就将 `promise.state` 置为 `fulfilled` 状态
2. 在 `promise.state === fulfilled` 状态时，同步执行到 `new promise.then.promise()`，在 `promise.then.promise` 的内部会同步执行 `let value = promise.then.cb` 函数
3. 然后执行 `promise.then.promise.resove(value)` 方法，这样 `promise.then.promise.state === fulfilled`，这样就继续 同步执行下去
4. 结果就是同步执行 `promise.then.promise.then.cb` 方法
5. 在 第三四步中的理解 就是简单的 当前 `then` 返回的 `promise` 去执行 链接下去 `then.cb` 方法而已，其实跟 那个异步的道理是一样的，即 每个 `promise` 负责收集 or 执行 `promise.then.cb`

```
enum PromiseStatus {
  '',
  Pendding,
  Fulfilled,
  Rejected
}
class MyPromise {
  private state: PromiseStatus;
  private fulfilledCallBacks = [];
  private value: any = null;
  constructor(executor) {
    this.state = PromiseStatus.Pendding;
    executor(this.resolve.bind(this));
  }
  private resolve(value?: any) {
    this.state = PromiseStatus.Fulfilled;
    this.value = value;

    this.fulfilledCallBacks.forEach(callback => {
      const ret = callback.cb(this.value);
      callback.resolve(ret);
    });
  }
  public then(fulfiledCb?: any, rejectedCb?: any) {
    return new MyPromise((resolve, reject) => {
      if (this.state === PromiseStatus.Pendding) {
        // 将 then 方法返回的 promise 的resolve 收集到 上一个调用者的 cb 队列当中
        // 这是实现 值穿透（值在几个promise间传递）的关键
        this.fulfilledCallBacks.push({
          resolve: resolve,
          cb: fulfiledCb
        });
      }

      if (this.state === PromiseStatus.Fulfilled) {
        // 执行当前 promise.then.cb
        const ret = fulfiledCb(this.value);
        // 执行 promise.then.promise.resolve
        // 将 promise.then.promise 的状态更改为 Fulfilled
        // 然后就可以同步执行 promise.then.promise.then.cb
        resolve(ret)
      }
    });
  }
}

new MyPromise(resolve => {
  console.log('start task')
  resolve('getUserId同步请求');
})
  .then(result => {
    return `prefix: ${result}`;
  })
  .then(res => {
    console.log(res);
  });
```

### 四、 完善 resolve 方法，处理 then.cb() 返回值为 一个 promise 情况

值穿透的一个要点是，将下一个循环的属性A拿到当前来进行寄存，在当前的任务处理完后，就可以在当前任务中将得到的结果传递给A

1. `new [promise-1]` `new [promise-2]`，传入 `[promise-1]` 与 `[promise-2]` 内部的 `executor` 均为异步
2. `[promise-1].then(callback1)`
    * `new [promise-3]`
    * `[promise-1].callbacks` 收集 `{resolve: [promise-3].resolve, onFulfilledCb: callback1 }` 
    * `callback1` 为 一个 `promise`
3. `[promise-3].then(callback3)`
    * `new [promise-4]`
    * `[promise-3].callbacks` 收集 `{resolve: [promise-4].resolve, onFulfilledCb: callback3}` 
    * `callback3` 为普通的同步执行的代码，无异步
4. 链式调用的 各个 `callback` 是同步执行的，所以可以一下子收集完成
5. `[promise-1]` 异步代码执行完毕，主动调用 `[promise-1].resolve(async result)`
    * `[promise-1].state = fulfilled`
    * `[promise-1].value = async result`
    * `[promise-1].callbacks` 循环调用  
        - 获取 `callback1` 的值，即 `let valuePromise = callback1(async result)`  
          注意，`valuePromise` 既是 `[promise-2]`
        - 值穿透，即 将 `valuePromise` 传递下去，也就是 `[promise-3].resolve(valuePromise)`
6. `[promise-3].resolve([promise-2])` 这时候就有点特殊了
    * 普通情况下，`resolve` 的值为 一个基本类型的话，当前的 `promise.state` 会 置为 `fulfilled` 状态
    * `[promise-3].resolve([promise-2])` 时，`[promise-3].state` 仍为 `pendding` 状态，也就没有 去执行 `[promise-3].callbacks` 队列了
    * `[promise-3].resolve([promise-2])` 时，执行的语句为 `[promise-2].then([promise-3].resolve)`，既是 将 `[promise-3].resolve)` 作为 `[promise-2]` 的 `callback`，即 `callback2 = [promise-3].resolve`
        - `new [promise-5]`
        - `[promise-2].callbacks` 收集 `{resolve: [promise-5].resolve, onFulfilledCb: [promise-3].resolve)}`
7. `[promise-2].resolve(async2 result)`
    * `[promise-2].state = fulfilled`
    * `[promise-2].value = async2 result`
    * `[promise-2].callbacks` 循环调用
        - stepone: 获取 `callback2` 的值，即 `let value = [promise-3].resolve(async2 result)`
        - steptwo: 值穿透，即 将 `value` 传递下去，也就是 `[promise-5].resolve(value)`
8. stepone: `[promise-3].resolve(async2 result)` 过程
    * `[promsie-3].state = fulfilled`
    * `[promise-3].value = async2 result`，这样就拿到了 `[promise-2]` 异步执行的结果！！！
    * `[promise-3].callbacks` 循环调用
        - 获取 `callback3` 的值，即 `let value = callback3(async result)`
        - 值穿透，即 将 `value` 传递下去，也就是 `[promise-4].resolve(value)`
9. `[promise-4].resolve(value)`
    * `[promise-4].state = fulfilled`
    * `[promise-4].value = value`
    * `[promise-4].callbacks` 队列为空，停止运行
10. steptwo: `[promise-5].resolve(value)`
    * `[promise-5].state = fulfilled`
    * `[promise-5].value = value`
    * `[promise-5].callbacks` 队列为空，停止运行

```
let promiseCount = 1;

enum PromiseStatus {
  '',
  Pendding,
  Fulfilled,
  Rejected
}
class MyPromise {
  private name = '';

  private state: PromiseStatus;
  private fulfilledCallBacks = [];
  private value: any = null;
  constructor(executor) {
    this.name = `Promse-${promiseCount++}`;
    console.info('[%s]:constructor', this.name);

    this.state = PromiseStatus.Pendding;
    executor(this.resolve.bind(this));
  }
  private resolve(value?: any) {
    console.info('[%s]:_resolve', this.name);
    console.info('[%s]:_resolve', this.name, 'value=', value);
    if (value instanceof MyPromise) {
      var then = value.then;
      if (typeof then === 'function') {
        then.call(value, this.resolve.bind(this));
        return;
      }
    }

    this.state = PromiseStatus.Fulfilled;
    this.value = value;

    this.fulfilledCallBacks.forEach(callback => {
      const ret = callback.onFulfilledCb(this.value);
      callback.resolve(ret);
    });
  }
  public then(fulfiledCb?: any, rejectedCb?: any) {
    console.info('[%s]:then', this.name);
    return new MyPromise((resolve, reject) => {
      if (this.state === PromiseStatus.Pendding) {
        // 将 then 方法返回的 promise 的resolve 收集到 上一个调用者的 cb 队列当中
        // 这是实现 值穿透（值在几个promise间传递）的关键
        this.fulfilledCallBacks.push({
          resolve: resolve,
          onFulfilledCb: fulfiledCb
        });
        console.info('[%s]:_handle', this.name, 'callbacks=', this.fulfilledCallBacks);
      }

      if (this.state === PromiseStatus.Fulfilled) {
        // 执行当前 promise.then.cb
        const ret = fulfiledCb(this.value);
        // 执行 promise.then.promise.resolve
        // 将 promise.then.promise 的状态更改为 Fulfilled
        // 然后就可以同步执行 promise.then.promise.then.cb
        resolve(ret);
      }
    });
  }
}

const mockAjax = (url, s, callback) => {
  // console.log('start async async')
  setTimeout(() => {
    callback(url + '异步请求耗时' + s + '秒');
    // console.log('end async task')
  }, 1000 * s);
};

const pUserId = new MyPromise(resolve => {
  mockAjax('getUserId', 1, function (result) {
    resolve(result);
  });
});
const pUserName = new MyPromise(resolve => {
  mockAjax('getUserName', 2, function (result) {
    resolve(result);
  });
});

pUserId
  .then(id => {
    console.log(id);
    return pUserName;
  })
  .then(name => {
    console.log(name);
  });
```

### 五、 参考资料的代码
```
let promiseCount = 1;
//完整的实现 测试Demo
class Promise {
    callbacks = [];
    name = '';
    state = 'pending'; //增加状态
    value = null; //保存结果
    constructor(fn) {
        this.name = `Promse-${promiseCount++}`;
        console.log('[%s]:constructor', this.name);
        fn(this._resolve.bind(this));
    }
    then(onFulfilled) {
        console.log('[%s]:then', this.name);
        return new Promise(resolve => {
            this._handle({
                onFulfilled: onFulfilled || null,
                resolve: resolve
            });
        });
    }
    _handle(callback) {
        // console.log('[%s]:_handle', this.name, 'state=', this.state);

        if (this.state === 'pending') {
            this.callbacks.push(callback);
            console.log('[%s]:_handle', this.name, 'callbacks=', this.callbacks);
            return;
        }
        //如果then中没有传递任何东西
        if (!callback.onFulfilled) {
            callback.resolve(this.value);
            return;
        }
        var ret = callback.onFulfilled(this.value);
        callback.resolve(ret);
    }
    _resolve(value) {
        console.log('[%s]:_resolve', this.name);
        console.log('[%s]:_resolve', this.name, 'value=', value);

        if (
            value &&
            (typeof value === 'object' || typeof value === 'function')
        ) {
            var then = value.then;
            if (typeof then === 'function') {
                then.call(value, this._resolve.bind(this));
                return;
            }
        }

        this.state = 'fulfilled'; //改变状态
        this.value = value; //保存结果
        this.callbacks.forEach(callback => this._handle(callback));
    }
}

/**
 * 模拟异步请求
 * @param {*} url
 * @param {*} s
 * @param {*} callback
 */
const mockAjax = (url, s, callback) => {
    setTimeout(() => {
        callback(url + '异步请求耗时' + s + '秒');
    }, 1000 * s);
};
const pUserId = new Promise(resolve => {
    mockAjax('getUserId', 1, function (result) {
        resolve(result);
    });
});
const pUserName = new Promise(resolve => {
    mockAjax('getUserName', 2, function (result) {
        resolve(result);
    });
});

pUserId
    .then(id => {
        console.log(id);
        return pUserName;
    })
    .then(name => {
        console.log(name);
    });
```
    
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
