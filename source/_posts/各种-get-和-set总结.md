---
title: 各种 get 和 set总结
date: 2018-07-09 22:28:52
category: js
---

### 一、 vue 计算属性当中的 get 与 set 勾子
```
calculateAttr() {
  return 'get值'
},
```

如上代码，当我试图更改 `calculateAttr` 值时，在控制台将会报错 ` Computed property "calculateAttr" was assigned to but it has no setter.`。 简介讲，我们只是 给 `calculateAttr` 设置了get属性，而未设置其set勾子。  
事实上，计算属性简单做字面理解既是：这个值是被计算出来的，来做展示的，而不是设置的。所以当我们想要设置计算属性的值的时候，应该单独的设置其set勾子。亦如vue文档中所说的  
> 计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter
```
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

对其值的设置也是借助与 `firstName lastName` 而进行的


### 二、 Object.defineProperty(obj, prop, descriptor)
[Object.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)  
```
var Book = {}
var tempName = '';
Object.defineProperty(Book, 'name', {
  set: function (value) {
    tempName = value;
    console.log('你取了一个书名叫做' + value);
  },
  get: function () {
    return '《' + tempName + '》'
  }
})

Book.name = 'vue权威指南';  // 你取了一个书名叫做vue权威指南
console.log(Book.name);  // 《vue权威指南》
```

与 vue 的计算属性差不多，只不过vue上面的计算属性 都是 `this` 对象的

### 三、 对象初始化器在创建对象的时候指明
```
let obj = {
  _age: 18,
  get age ()  {
    return `I'm ${this._age} years old`
  },
  set age (val) {
    this._age = Number(val)
  }
}

console.log(obj.age) // I'm 18 years old
obj.age = 19
console.log(obj.age) // I'm 19 years old
```

### 四、 Proxy 代理  
[拿Proxy可以做哪些有意思的事儿](https://segmentfault.com/a/1190000015009255)  
与对象本身自有的get勾子不同地方在于。对象自有的get主要是针对当前对象并不存在的key，get 可以生成这个key(如果get的是已存在的key，则读取的时候是读get的值)  
而代理 如其名一般，只要你对对象设置了代理，那么，当读取该对象属性的时候自然会访问代理
访问代理的前提是，你为该对象设置了代理  
而 `Object.defineProperty` 说白了就是 给 对象定义一些属性而已  
而代理 是说，我们 想要 访问 某个对象的时候，我们不直接访问这个对象，而是 访问这个对象的代理，这这个代理中，我们设置下，当我们访问某些属性的时候 需要发生的事情
```
let target = { age: 18, name: 'Niko Bellic' }
let handlers = {
  get (target, property) {
    return `${property}: ${target[property]}`
  },
  set (target, property, value) {
    target[property] = value
  }
}
let proxy = new Proxy(target, handlers)

proxy.age = 19
console.log(target.age, proxy.age)   // 19,          age : 19
console.log(target.name, proxy.name) // Niko Bellic, name: Niko Bellic
```

解决对象属性为undefined的问题
```
let target = {}
let handlers = {
  get: (target, property) => {
    target[property] = property in target ? target[property] : {}
      if (typeof target[property] === 'object') {
        return new Proxy(target[property], handlers)  
        // 如果读取的某一key值也是一个对象（A对象），那么为 A对象 设置代理  
        // 从而当读取A对象的某一属性的时候，也会走代理，执行此get勾子
      }
    return target[property]
  }
}
let proxy = new Proxy(target, handlers)
// console.log('z' in proxy.x.y) // false (其实这一步已经针对`target`创建了一个x.y的属性)
// proxy.x 默认调用了 get 方法（生成了针对该key的value值（即一个空对象））
// 而 在 .y 的话，如果不在继续代理，就没有调用get,返回的自然是一个 undefied
// proxy.x.y.z = 'hello'
// console.log('z' in proxy.x.y) // true
// console.log(target.x.y.z) // hello
```


### 五、 作用于类属性的装饰器
[Javascript 中的装饰器](https://aotu.io/notes/2016/10/24/decorator/index.html)  
装饰器在作用于属性的时候，实际上是通过 Object.defineProperty 来进行扩展和封装的
```
function readonly(target, name, descriptor) {
    discriptor.writable = false;
    return discriptor;
}
class Cat {
    @readonly
    say() {
        console.log("meow ~");
    }
}
var kitty = new Cat();
kitty.say = function() {
    console.log("woof !");
}
kitty.say()    // meow ~
```
上面的这段代码中，装饰器实际的作用形式是这样的：
```
let descriptor = {
    value: function() {
        console.log("meow ~");
    },
    enumerable: false,
    configurable: true,
    writable: true
};
descriptor = readonly(Cat.prototype, "say", descriptor) || descriptor;
Object.defineProperty(Cat.prototype, "say", descriptor);
```
