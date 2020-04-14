---
title: Proxy
date: 2020-04-14 10:55:15
tags: [js, doc]
---

### 一、 定义

Proxy 对象用于定义基本操作的自定义行为（如属性查找、赋值、枚举、函数调用等）。  
The Proxy object is used to define custom behavior for fundamental operations (e.g. property lookup, assignment, enumeration, function invocation, etc).

1. handler 包含陷阱（trap）的占位符对象，可译为处理器对象。  
   handler 对象各个属性通常为函数，这些函数构成了对 被代理对象的 捕获器(trap)
2. traps 提供属性访问的方法。这类似于操作系统中捕获器的概念，一般直译作陷阱。
3. target 被 Proxy 代理虚拟化的对象。它常被作为代理的存储后端。根据目标验证关于对象不可扩展性或不可配置属性的不变量（保持不变的语义）。

### 二、 一个完整的 traps 列表示例

```
var docCookies = new Proxy(docCookies, {
  "get": function (oTarget, sKey) {
    return oTarget[sKey] || oTarget.getItem(sKey) || undefined;
  },
  "set": function (oTarget, sKey, vValue) {
    if (sKey in oTarget) { return false; }
    return oTarget.setItem(sKey, vValue);
  },
  "deleteProperty": function (oTarget, sKey) {
    if (sKey in oTarget) { return false; }
    return oTarget.removeItem(sKey);
  },
  "enumerate": function (oTarget, sKey) {
    return oTarget.keys();
  },
  "ownKeys": function (oTarget, sKey) {
    return oTarget.keys();
  },
  "has": function (oTarget, sKey) {
    return sKey in oTarget || oTarget.hasItem(sKey);
  },
  "defineProperty": function (oTarget, sKey, oDesc) {
    if (oDesc && "value" in oDesc) { oTarget.setItem(sKey, oDesc.value); }
    return oTarget;
  },
  "getOwnPropertyDescriptor": function (oTarget, sKey) {
    var vValue = oTarget.getItem(sKey);
    return vValue ? {
      "value": vValue,
      "writable": true,
      "enumerable": true,
      "configurable": false
    } : undefined;
  },
});

/* Cookies 测试 */

alert(docCookies.my_cookie1 = "First value");
alert(docCookies.getItem("my_cookie1"));

docCookies.setItem("my_cookie1", "Changed value");
alert(docCookies.my_cookie1);
```

### 三、 handler 对象的方法

handler 对象是一个容纳一批特定属性的占位符对象。它包含有 Proxy 的各个捕获器（trap）。  
所有的陷阱是可选的。**如果没有定义某个陷阱，那么就会保留源对象的默认行为**。

#### 1) handler.get(): A trap for getting property values.

```
function get(obj, defaultVal) {
    return new Proxy(obj, {
        get(target, prop) {
            if (
                Object.prototype.toString.call(target[prop]) ===
                '[object Object]'
            ) {
                return get(target[prop], defaultVal);
            } else {
                return target[prop] || defaultVal;
            }
        }
    });
}
```

#### 2) handler.set(): A trap for setting property values.

拦截设置，做校验

```
// 验证规则
let validators = {
    name: {
        validate(value) {
            return value.length > 6;
        },
        message: '用户名长度不能小于六'
    },
    moblie: {
        validate(value) {
            return /^1(3|5|7|8|9)[0-9]{9}$/.test(value);
        },
        message: '手机号格式错误'
    }
};

validators = new Proxy(validators, {
    set(target, prop, value) {
        if (!target[prop].validate(value)) {
            throw new Error(target[prop].message);
        }
    }
});

validators.name = '1212321321';
validators.moblie = '25110240955'; // Error: 手机号格式错误
```

#### 3) handler.apply(target, context, args): A trap for a function call.

拦截函数的调用，交由 `handler.apply` 函数内部如何执行这个 函数

1. `target` 被代理的函数
2. `context` 为函数执行的上下文
3. `args` 为一个数组，即传入给 代理对象的参数 会被自动转换为一个数组

```
function test() {
    console.log('this is a test function');
}
const obj = {
    name: '123'
};
const func = new Proxy(test, {
    apply(target, context, args) {
        console.log(context.name); // 123
        console.log(args); // [ 'a', 'b', 'c' ]
        target.apply(context, args); // this is a test function
    }
});
func.call(obj, 'a', 'b', 'c');
```

#### 4) handler.construct(target, args, newTarget): A trap for the new operator.

拦截 构造函数实例化的过程，交由 `handler.construct` 内部执行具体的 构造函数实例话的过程

1. `target` 为被代理的 构造函数
2. `args` 为数组，即传入给构造函数的参数被转换为也给数组
3. `newTarget` 为 Proxy 对象，即被代理后返回的对象

```
function Person(name, age) {
    this.name = name;
    this.age = age;
}
const P = new Proxy(Person, {
    construct(target, args, newTarget) {
        target.prototype.log = 'logs';
        return new target(...args);
    }
});
const p = new P('tom', 21);
console.log(p.name); // 'tom'
console.log(p.age); // 21
console.log(p.log); // 'logs'
```

#### 5) handler.defineProperty(): A trap for Object.defineProperty.

拦截了 `Object.defineProperty(obj, key, descriptor)` 的行为，可以重新赋值

```
let obj = {
    name: 'origin name'
};

obj = new Proxy(obj, {
    defineProperty(target, prop, descriptor) {
        console.log(target, prop, descriptor);
        return Reflect.defineProperty(target, prop, {
            set(val) {
                return (target[prop] = val);
            },
            get() {
                return 'prefix: ' + descriptor.get();
            }
        });
    }
});

Object.defineProperty(obj, 'name', {
    get() {
        return 'Object.defineProperty has changed obj.name';
    }
});

// obj.name = '12321';
console.log(obj.name);
```
