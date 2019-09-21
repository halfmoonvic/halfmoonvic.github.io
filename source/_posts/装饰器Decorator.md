---
title: 装饰器Decorator
date: 2018-11-21 18:31:57
tags:
---

@b('argarg') 相当于 A = b('argarg')(A)  
装饰器在作用在类上的时候便已经执行完 最开始的函数，装饰器隐形的将 被装饰者作为最后一个参数传入进 装饰函数并执行
```
const b = (arg) => Comp => {
    return class C {
        constructor() {
            console.log('ccccccc')
        }
        say() {
            console.log(`i am ${arg}`)
        }
    }
}

@b('argarg')
class A {
    constructor() {
        console.log('aaa')
    }
}

// let oth = b('feafea')(A)

let aha = new A()
aha.say()
```


```
let func1 = (arg1) => (arg2) => {
    return {
        'a': arg1,
        'b': arg2
    }
}

function func2(arg1) {
    return function(arg2) {
        return {
            'c': arg1,
            'd': arg2
        }
    }
}
console.log(func1('aaaa')('bbb'), func2('ccc')('dddd'))
```
