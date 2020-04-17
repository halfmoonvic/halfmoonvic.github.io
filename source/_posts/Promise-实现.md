---
title: Promise 实现
date: 2020-04-17 10:17:03
category: js
tags: [promise, 异步, doc]
---

### 一、 乞丐版 promise

仅实现 非常乞丐版的 promise，仅有个形式  
运作原理既是

1. `then` 方法负责收集 `thenCb` 回调函数
2. `resolve` `reject` 则是负责处理收集过来的
3. 为了 保证 在 `resolve` `reject` 处理回调队列前，回调队列收集完毕，在 `new Promise` 时，加了一个延时，即 `exector` 也并不是立即执行函数

``` typescript
// the res is the value from resolve or reject
// or itself return value
type thenCb = (res: any) => any | void;

class MyPromise {
  private value: any = null;
  private fulfilledCallbacks: thenCb[] = [];
  private rejectedCallbacks: thenCb[] = [];

  // 需要加一个 setTimeout 以便在 new Promise 时
  // 不立即执行 resolve 方法，也就是 不去执行 _flushQueue
  // 以便给 then 方法去收集 回调的机会
  constructor(exector) {
    setTimeout(() => {
      exector(this.resolve.bind(this), this.reject.bind(this));
    });
  }

  private resolve(value: any): void {
    this.value = value;
    this._flushQueue(this.fulfilledCallbacks);
  }

  private reject(error: any): void {
    this.value = error;
    this._flushQueue(this.rejectedCallbacks);
  }

  private _flushQueue(queue: thenCb[]) {
    while (queue.length) {
      const fulfilledCb = queue.shift();
      this.value = fulfilledCb(this.value);
    }
  }

  public then(onFulfilled?: thenCb, onRejected?: thenCb): MyPromise {
    if (onFulfilled) {
      this.fulfilledCallbacks.push(onFulfilled);
    }
    if (onRejected) {
      this.rejectedCallbacks.push(onRejected);
    }
    return this;
  }

  public catch(onRejected?: thenCb): MyPromise {
    return this.then(undefined, onRejected);
  }
}

let p = new MyPromise((resolve, reject) => {
  resolve('resolved');
  // reject('rejected');
});

p.then(res => {
  console.log('this is first then received: ', res);
  return 'first then change res —— ' + res;
}).then(res => {
  console.log('this is seond then received: ', res);
}).catch(err => {
  console.log(err);
});
```

### 二、 实现 promise 的 核心方法 then 的部分

1. `then` 方法返回一个全新的 `promise`

    - 由于 返回的是一个 全新的 `promise`，这就导致了我们在进行如上例当中的链式调用的时候，`thenCb` 是被收集到不同的 `promise.callbacks` 当中去了，也就不能使用 如上面的 `_flushQueue` 方法
    - 思路是 `promiseA` 中收集 下一个 `promiseB` 的 `resolveB` 方法
    - 当 `promiseA.resolveA(value)` 后，`promiseA.callbacks` 队列执行，`promiseA.callbacks` 的 `this.value = callback(value)` 执行，
    - 随后就是 调用 `promiseB.resolve(this.value)`
    - `promiseB.resolve` 的执行，又会 调用 `promiseB.callbacks`，即 `this.value = callback(this.value)` 继而去执行 `promiseC.resolve(this.value)`
    - 从而又能实现 `value` 一直传递下去

<details>
<summary>Click More</summary>
``` typescript
enum PromiseStatus {
    '',
    Pendding,
    Fulfilled,
    Rejected
}

// the res is the value from resolve or reject
// or itself return value
type thenCb = (res: any) => any | void;

interface ResolveCb {
    cb?: thenCb;
    resolve: (value: any) => void;
}
interface RejectCb {
    cb?: thenCb;
    reject: (value: any) => void;
}

class MyPromise {
    private value: any = null;
    private fulfilledCallbacks: ResolveCb[] = [];
    private rejectedCallbacks: RejectCb[] = [];

    constructor(exector) {
        exector(this.resolve.bind(this), this.reject.bind(this));
    }

    private resolve(value: any): void {
        this.value = value;
        this.fulfilledCallbacks.forEach(resolveCb => {
            // 如果当前的 promiseA 没有相应的 cb
            // 则直接将 this.value 传递给 promiseA.callback 所收集的 下一个 promiseB.resolve
            // 下一个 promiseB.resolve 好去继续调用 下一个 promiseB.callback 队列
            // 如此实现，即便隔着几个 promise，也能将 相应的值传递出去
            if (resolveCb.cb) {
                this.value = resolveCb.cb(this.value);
            }
            resolveCb.resolve(this.value);
        });
    }

    private reject(err: any): void {
        this.value = err;
        this.rejectedCallbacks.forEach(rejectCb => {
            if (rejectCb.cb) {
                this.value = rejectCb.cb(this.value);
            }
            rejectCb.reject(this.value);
        });
    }

    public then(onFulfilled?: thenCb, onRejected?: thenCb): MyPromise {
        return new MyPromise((resolve, reject) => {
            this.fulfilledCallbacks.push({
                resolve,
                cb: onFulfilled
            });
            this.rejectedCallbacks.push({
                reject,
                cb: onRejected
            });
        });
    }

    public catch(onRejected?: thenCb): MyPromise {
        return this.then(undefined, onRejected);
    }
}

let p1 = new MyPromise((resolve, reject) => {
    setTimeout(() => {
        // resolve('resolved');
        reject('rejected');
    }, 20);
});

p1.then(res => {
    console.log('this is first then received: ', res);
    return 'first then change res —— ' + res;
})
    .then(res => {
        console.log('this is seond then received: ', res);
    })
    .catch(err => {
        console.log(err);
    });
```
</details>


2. 移除 `setTimeout`

    - 当 `promise` 内部执行的代码为 异步时，`then` 方法可以实现 相应 `callbacks` 收集
    - 当 `promise` 内部执行的代码为 同步时，则在各个 `promise` `resolve` 后，直接运行 `then` 中传入的回调，中间若没有异步行为，则全部同步的执行
    - 这个时候就需要接入状态管理控制了
        * 即在 `resolve` 之前 `promise` 为 `Pendding` 状态，这种情况下 不可以调用 `promise.callbacks`
        * 在 `resolve` 之后 `promise` 为 `Fulfilled` or `Rejected`，这种情况下 则可以立即调用 相应 `promise.callbacks`

<details>
<summary>Click More</summary>
``` typescript
enum PromiseStatus {
    '',
    Pendding,
    Fulfilled,
    Rejected
}

// the res is the value from resolve or reject
// or itself return value
type thenCb = (res: any) => any | void;

interface ResolveCb {
    cb?: thenCb;
    resolve: (value: any) => void;
}
interface RejectCb {
    cb?: thenCb;
    reject: (value: any) => void;
}

class MyPromise {
    private value: any = null;
    private state: PromiseStatus = 0;
    private fulfilledCallbacks: ResolveCb[] = [];
    private rejectedCallbacks: RejectCb[] = [];

    constructor(exector) {
        this.state = PromiseStatus.Pendding;
        exector(this.resolve.bind(this), this.reject.bind(this));
    }

    private resolve(value: any): void {
        if (this.state === PromiseStatus.Pendding) {
            this.state = PromiseStatus.Fulfilled;
            this.value = value;
        }

        this.fulfilledCallbacks.forEach(resolveCb => {
            // 如果当前的 promiseA 没有相应的 cb
            // 则直接将 this.value 传递给 promiseA.callback 所收集的 下一个 promiseB.resolve
            // 下一个 promiseB.resolve 好去继续调用 下一个 promiseB.callback 队列
            // 如此实现，即便隔着几个 promise，也能将 相应的值传递出去
            if (resolveCb.cb) {
                this.value = resolveCb.cb(this.value);
            }
            resolveCb.resolve(this.value);
        });
    }

    private reject(err: any): void {
        if (this.state === PromiseStatus.Pendding) {
            this.state = PromiseStatus.Rejected;
            this.value = err;
        }

        this.rejectedCallbacks.forEach(rejectCb => {
            if (rejectCb.cb) {
                this.value = rejectCb.cb(this.value);
            }
            rejectCb.reject(this.value);
        });
    }

    public then(onFulfilled?: thenCb, onRejected?: thenCb): MyPromise {
        return new MyPromise((resolve, reject) => {
            if (this.state === PromiseStatus.Pendding) {
                this.fulfilledCallbacks.push({
                    resolve,
                    cb: onFulfilled
                });
                this.rejectedCallbacks.push({
                    reject,
                    cb: onRejected
                });
            }
            if (this.state === PromiseStatus.Fulfilled) {
                if (onFulfilled) {
                    this.value = onFulfilled(this.value);
                }
                resolve(this.value);
            }
            if (this.state === PromiseStatus.Rejected) {
                if (onRejected) {
                    this.value = onRejected(this.value);
                }
                reject(this.value);
            }
        });
    }

    public catch(onRejected?: thenCb): MyPromise {
        return this.then(undefined, onRejected);
    }
}

let p1 = new MyPromise((resolve, reject) => {
    resolve('resolved');
    // promise 状态 仅能 从 pendding 转变为 Fulfilled or Rejected
    // 上面代码执行后，下面的 reject 也不能更改状态了
    reject('rejected');
});

p1.then(res => {
    console.log('this is first then received: ', res);
    return 'first then change res —— ' + res;
})
    .then(res => {
        console.log('this is seond then received: ', res);
    })
    .catch(err => {
        console.log(err);
    });
```
</details>

### 三、 完善 resolve、reject 方法，处理 callback 返回值为 一个 promise 情况
当 `callback` 返回值 也是一个 `promise` 的时候，这个时候需要将 `promise.resolve(value)` 的 `value` 值传递给 `promise` 链条中去
情况:  

1. `PromiseA.then` 的返回值是 `PromiseB`
2. `PromiseA.then(cb)` 中的 `cb` 的返回值为 `Promise0`
3. 现在的目的则是 将 `Promise0.resolve(value)` 的 `value` 传递给 `PromiseB`

具体执行流程：  

1. `PromiseA.resolve(valueA)` 后，无论 `PromiseA` 中是同步还是 异步，都会先执行 `PromiseA.then(callback)` 当中的 `callback`
2. 也就是 `return Promise0`
3. 随后 执行 `PromiseB.resolve(Promise0)` 以便 执行 `PromiseB.callbacks`
4. `PromiseB.resolve` 执行时是需要 获取 `Promise0.resolve` 的值，这个时候 就没有直接 执行 `PromiseB.callbacks`
5. 而是执行 `Promise0.then(PromiseB)` 这样 `Promise0.callbacks` 就收集到了 `PromiseB.resolve`
6. 在 `Promise0.resolve` 之后，执行 `Promise0.callbacks` 也就是执行 `PromiseB.resolve(value0)`
7. 这样 链条又重新链接起来，`PromiseB.resolve(value0)` 后 执行相应的 `PromiseB.callbacks`

``` typescript
enum PromiseStatus {
    '',
    Pendding,
    Fulfilled,
    Rejected
}

// the res is the value from resolve or reject
// or itself return value
type thenCb = (res: any) => any | void;

interface ResolveCb {
    cb?: thenCb;
    resolve: (value: any) => void;
}
interface RejectCb {
    cb?: thenCb;
    reject: (value: any) => void;
}

let promiseCount = 1;

class MyPromise {
    private name = '';

    private value: any = null;
    private state: PromiseStatus = 0;
    private fulfilledCallbacks: ResolveCb[] = [];
    private rejectedCallbacks: RejectCb[] = [];

    constructor(exector) {
        this.name = `Promse-${promiseCount++}`;
        console.info('[%s]:constructor', this.name);

        this.state = PromiseStatus.Pendding;
        exector(this.resolve.bind(this), this.reject.bind(this));
    }

    private resolve(value: any): void {
        console.info('[%s]:_resolve', this.name);
        console.info('[%s]:_resolve', this.name, 'value=', value);

        if (value instanceof MyPromise) {
            const then = value.then;
            // then.(this.resolve.bind(this))
            then.call(value, this.resolve.bind(this), this.reject.bind(this));
            return;
        }

        if (this.state === PromiseStatus.Pendding) {
            this.state = PromiseStatus.Fulfilled;
            this.value = value;
        }

        this.fulfilledCallbacks.forEach(resolveCb => {
            // 如果当前的 promiseA 没有相应的 cb
            // 则直接将 this.value 传递给 promiseA.callback 所收集的 下一个 promiseB.resolve
            // 下一个 promiseB.resolve 好去继续调用 下一个 promiseB.callback 队列
            // 如此实现，即便隔着几个 promise，也能将 相应的值传递出去
            if (resolveCb.cb) {
                this.value = resolveCb.cb(this.value);
            }
            resolveCb.resolve(this.value);
        });
    }

    private reject(err: any): void {
        if (err instanceof MyPromise) {
            const then = err.then;
            // then.(this.resolve.bind(this))
            then.call(err, this.resolve.bind(this), this.reject.bind(this));
            return;
        }

        if (this.state === PromiseStatus.Pendding) {
            this.state = PromiseStatus.Rejected;
            this.value = err;
        }

        this.rejectedCallbacks.forEach(rejectCb => {
            if (rejectCb.cb) {
                this.value = rejectCb.cb(this.value);
            }
            rejectCb.reject(this.value);
        });
    }

    public then(onFulfilled?: thenCb, onRejected?: thenCb): MyPromise {
        console.info('[%s]:then', this.name);
        return new MyPromise((resolve, reject) => {
            if (this.state === PromiseStatus.Pendding) {
                this.fulfilledCallbacks.push({
                    resolve,
                    cb: onFulfilled
                });
                this.rejectedCallbacks.push({
                    reject,
                    cb: onRejected
                });
                console.info('[%s]:_handle', this.name, 'callbacks=', this.fulfilledCallbacks);
            }
            if (this.state === PromiseStatus.Fulfilled) {
                if (onFulfilled) {
                    this.value = onFulfilled(this.value);
                }
                resolve(this.value);
            }
            if (this.state === PromiseStatus.Rejected) {
                if (onRejected) {
                    this.value = onRejected(this.value);
                }
                reject(this.value);
            }
        });
    }

    public catch(onRejected?: thenCb): MyPromise {
        return this.then(undefined, onRejected);
    }
}

const mockAjax = (url, s, callback) => {
    // console.log('start async async')
    setTimeout(() => {
        callback(url + '异步请求耗时' + s + '秒');
        // console.log('end async task')
    }, 1000 * s);
};

const pUserId = new MyPromise((resolve, reject) => {
    mockAjax('getUserId', 1, function (result) {
        resolve(result);
    });
});
const pUserName = new MyPromise((resolve, reject) => {
    mockAjax('getUserName', 2, function (result) {
        resolve(result);
    });
});

pUserId
    .then(id => {
        console.log('id', id);
        return pUserName;
    })
    .then(name => {
        console.log('name', name);
    })
```

上例中具体的执行流程为： 

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




### 四、 参考资料的代码
<details>
<summary>Click More</summary>
``` javascript
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
</details>

### 五、 参考资料
1. [图解 Promise 实现原理（二）—— Promise 链式调用](https://zhuanlan.zhihu.com/p/102017798)
2. [【翻译】Promises/A+规范](https://www.ituring.com.cn/article/66566)
