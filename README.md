1. 网络相关
2. js相关
3. vue
4. ES6
5. 其他
## 网络相关

### TCP和UDP的区别

TCP是面向有连接型，UDP是面向无连接型。

TCP是一对一传输，UDP支持一对一，一对多，多对一，多对多的交互通信。

TCP是面向字节流的，即把应用层传来的报文看成字节流，将字节流拆分成大小不等的数据块，并添加TCP首部。UDP是面向报文的，对应用层传下来的报文不拆分也不合并，仅添加UDP首部。

TCP支持传输可靠性的多种措施。UDP仅提供最基本的数据传输能力。

### 
### TCP三次握手

### http和https

1、http是超文本传输协议，信息是明文传输，https是具有安全协议的ssl加密传输协议

2、https协议需要添加申请证书，一般免费的证书少，因此需要一定得费用。

3、http和https使用完全不同的连接方式，用的端口也不一样，http是80端口，https是443端口

4、http连接是无状态的，https是ssl+http构建的可进行加密传输，身份认证的网络协议，安全性高于http协议。

### get和post的区别

1、安全性post高于get，因为get的参数在url里就能够获取。

2、post可以传输的数据要比get多，get传输数据受字节长度限制

3、get和post支持的数据格式不同get只能支持json格式，post支持json form等格式

4、get比post更快，所以一般请求用get，提交用post

5、get支持缓存，post不支持缓存

### xss和csrf

XSS：跨站脚本攻击，CSRF：客户端请求伪造，ssrf服务端请求伪造

## HTML相关

## CSS相关

## js相关

### 闭包

闭包，可以访问另一个函数内部作用域的局部变量的函数，可以称为闭包。好处：保护函数的私有变量不受外部影响、形成不被销毁的栈内存、将上级作用域的引用保存下来，实现方法或属性的私有化，上层函数作用域清除的时候，闭包仍然会保留。不会被JS垃圾回收机制清除。缺点：由于过多的这种上级作用域的引用，容易导致内存泄漏。

使用场景：当我们进行封装的时候使用return返回函数；当函数作为参数来使用的时候；IIFE自执行函数；当我们使用定时器setTimeout；几乎所有的回调函数；函数的节流防抖；

### 垃圾回收机制

垃圾回收机制：标记清除法（标记阶段、清除阶段）从根对象进行遍历，对能访问到达的对象进行标记称为标记阶段，发现没有被标记的对象，则清空它的内存。引用计数法（了解即可，已弃用）。

### 原型和原型链以及构造函数

原型链，概念：每个对象都有_proto_属性，指向该对象的构造函数的原型对象（prototype）,每个函数都有prototype属性（属性的值也是一个对象，也有自己的prototype）。当我们访问一个对象（实例）的某个属性的时候，首先会从对象内部查找，如果没有，则会在它的_proto_(指向的是该对象的构造函数constructor的原型对象（prototype）上面查找)，如果没找到，则会在原型对象对象的proto上面查找，这样一层一层的查找的过程类似于一种链条，称之为原型链。V

### this

### 事件循环

事件循环其实就是javascript处理同步任务和异步任务的一种规则，js是单线程的，意味着同一时刻只能有一个任务进行。首先来明确一下同步任务和异步任务。

同步任务：script代码块也就是事件循环的起点，作为第一个宏任务启动事件循环。注意promise的定义属于同步任务。

异步任务又分为宏任务和微任务。

常见的宏任务：setTimeout回调（最常见，面试题掌握这个就够了）、setInteval回调、script代码块

常见的微任务：Process.nextTick回调、Promise.resolve().then回调、MutationObserver 等（主要记住这几个就足够了）

规则：从代码块开始，将代码块中的可以打印的同步任务全部先行打印出来，然后将最外层的宏任务和微任务依此压入对应的宏任务队列和微任务队列中。代码块第一轮执行完之后会产生一个宏任务队列和一个微任务队列，由于代码块也是属于宏任务的一种，则现在开始清空当前的所有微任务队列，清空完了之后则取出宏任务队列的下一个宏任务，完成之后查看有没有生成的微任务队列，有则清空，没有则继续下一个宏任务。直到结束。

示例一：

```typescript
console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0);

Promise.resolve().then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
}); // 这里作为微任务，如果一直产生新的微任务则永远不会进入到下一个宏任务，这里会一直链式调用

console.log('script end');



```
输出script start ->script end -> promise1 -> promise2 -> setTimeout
分析：代码块开始执行（第一个宏任务）-> 打印script start ->遇到setTimeout（宏任务）了，压入宏任务队列 -> 遇到Promise了，压入微任务队列 -> 打印script end  到此代码块（第一个宏任务）执行完毕，产生了两个队列，宏任务队列[setTimeout]，微任务队列[Promise.then],开始清空微任务队列->依此执行打印promise1、promise2。然后从宏任务队列中抽取下一个也就是setTimeout执行-> 打印setTimeout。

示例2：

```typescript
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})

```
打印：1、7、6、8、2、4、3、5、9、11、10、12
### 
### 事件代理

### 继承

原型链继承：优点是父类方法可以复用到每一个子类，可以访问父类的原型链上的方法属性。缺点是当某一个子类改变了父类的某一个属性的引用的时候，所有的子类都会受到影响。并且无法进行参数的传递。

```typescript
function Parent() {
  this.info = {
    name: "yhd",
    age: 19,
  }
}
function Child(){
  
}
child.prototype = new Parent()
```
构造函数继承：优点可以传参，父类的引用属性不会被共享。缺点：访问不到父类原型链上的属性方法。
```typescript
function Parent(name) {
    this.info = { name: name };
}
function Child(name){
  Parent.call(this,name)
}
let child1 = new Child("yhd");
```
组合式继承：优点支持父类原型链上方法属性的继承，父类引用属性不会被共享。可以给父类传参。缺点：会执行两次父类构造函数。
```typescript
function Parent(name){
  this.name = name
}
function Child(name){
  Parent.call(this,name);
}
Child.prototype = new Parent()
```
原型继承
寄生式继承

组合寄生式继承

## vue

### 生命周期（高频）

#### beforeCreate

这个阶段 vue 实例刚刚在内存中创建，此时 data 和 methods 这些都没初始化好

#### created

这个阶段 vue 实例在内存中已经创建好了，data 和 methods 也能够获取到了，但是模板还没编译

#### beforeMount

这个阶段完成了模板的编译，但是还没挂载到页面上

#### mounted

这个阶段，模板编译好了，也挂载到页面中了，页面也可以显示了

#### beforeUpdate

转态更新之前执行此函数，此时 data 中数据的状态值已经更新为最新的，但是页面上显示的数据还是最原始的，还没有重新开始渲染 DOM 树。

#### updated

这个阶段是转态更新完成后执行此函数，此时 data 中数据的状态值是最新的，而且页面上显示的数据也是最新的，DOM 节点已经被重新渲染了。

#### beforeDestroy

beforeDestroy 阶段处于 vue 实例被销毁之前，当然，这个阶段 vue 实例还能用。

#### destroyed

这个阶段在 vue 实例销毁后调用，此时所有实例指示的所有东西都会解除绑定，事件监听器也都移除，子实例也被销毁。

### 组件通讯

### MVVM的理解

### vue2响应式原理

注意：vue2中双向数据绑定是通过Object.defineproperty实现，它有一个getter来负责依赖收集，setter就是当数据发生更改的时候做一个通知。

vue中的响应式是通过发布订阅模式结合数据劫持来实现的。模板通过订阅这个数据，通过Object.defineproperty来劫持数据的getter和setter，数据发生变化的时候会通知订阅过这个数据的模板进行更新。

 

基础实现

```typescript
// 订阅器模型
let Dep = {
  clientList: {}, // 容器
  // 添加订阅
  listen:function(key,fn){
  // 当添加订阅的时候，如果没有订阅过，则给他以一个空的容器
    if(!this.clientList[key]){
      this.clienList[key] = []
    }
    this.clienList[key].push(fn)
  },
  // 发布
  trigger:function(){
    let key = Array.prototype.shift.call(arguments),
    fns = this.clientList[key];
    if(!fns||fns.length===0){
      return false
    }
    for(let i = 0;i<fns.length,i++){
      fns[i].apply(this,arguments)
    }
  }
}
```
### v-model的实现原理

### vue中的data()为什么是个函数

vue中的data()实际上利用的闭包的原理，使每一个组件都有自己的私有域，组件之间的变量不会相互影响。

### v-if和v-show的区别

v-if：元素真实渲染；

v-show：元素的display设置为true或者false

当需要多次显示隐藏的时候建议用v-show。v-show不能用于权限的操作。

### Vue模板编译的原理

分为3个阶段，解析阶段、优化阶段、生成阶段。

**解析阶段**：使用大量的正则表达式对template字符串进行解析，将标签、指令、属性等转化为抽象语法树AST。

 

**优化阶段**：遍历AST，找到其中的一些静态节点并进行标记，方便在页面重渲染的时候进行diff比较时，直接跳过这一些静态节点，优化runtime的性能。

 

**生成阶段**：将最终的AST转化为render函数字符串

### SSR

SSR也就是服务端渲染，也就是将Vue在客户端把标签渲染成HTML的工作放在服务端完成，然后再把html直接返回给客户端

 

SSR的优势：

* 更好的SEO
* 首屏加载速度更快
SSR的缺点：

* 开发条件会受到限制，服务器端渲染只支持beforeCreate和created两个钩子；
* 当需要一些外部扩展库时需要特殊处理，服务端渲染应用程序也需要处于Node.js的运行环境；
* 更多的服务端负载。
### 指令

```typescript
// vue2指令
// 钩子函数
bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
inserted：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。
我们会在稍后讨论渲染函数时介绍更多 VNodes 的细节。
componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用。
unbind：只调用一次，指令与元素解绑时调用。
参数
el、binding、vnode 和 oldVnode

// vue3指令
// 钩子函数
created() {},
  // 在绑定元素的父组件挂载之前调用
  beforeMount() {},
  // 在绑定元素的父组件挂载之后调用
  mounted() {},
  // 在包含组件的 VNode 更新之前调用
  beforeUpdate() {},
  // 在包含组件的 VNode 及其子组件的 VNode 更新之后调用
  updated() {},
  // 在绑定元素的父组件卸载之前调用
  beforeUnmount() {},
  // 在绑定元素的父组件卸载之后调用
  unmounted() {}
  // 参数
  
// 全局指令 
// 创建文件进行指令的编写，在项目的入口js中进行引入
例子
..debounce.ts // 创建全局指令，绑定在按钮上，在dom mounted的时候进行绑定。规定按钮多少时间内只能点击一次。即对按钮进行防抖。
export default {
    created(el: HTMLElement | any, binding:any,vnode:any) {
        let timer: any = null;
        el.addEventListener('click', () => {
            if (timer) {
              clearTimeout(timer);
            }
// fn是需要执行的函数，time是传入的时间间隔
            timer = setTimeout(binding.value.fn, binding.value.time);
        });
    },
};
.. main.js
import Debounce from './debounce'
app.directive('debounce', Debounce);

// 使用
test.vue
<div v-debounce="{fn:testfun,time:2000}">点击</div>
```

## ES6

### ES6中的箭头函数

箭头函数语法更加简洁

箭头函数不会创建自己的this，箭头函数的this指向在定义的时候继承自最外层第一个普通函数的this，所以箭头函数的this在他创建的时候就已经确定了，之后就不会再发生改变了

call、bind、apply都不能改变箭头函数的this指向

箭头函数不能作为构造函数使用。（new会首先生成一个空对象、再把函数中的this指向这个对象，然后执行构造函数中的语句、返回该对象实例）

箭头函数没有arguements、他是用rest参数来代替。所以在箭头函数中访问arguments对象实际上是访问的是外部函数执行环境中的this。

### Promise

```typescript
const PENDING = "pending";
        const FULFILLED = "fulfilled";
        const REJECTED = "rejected";
        class MyPromise {
            constructor(handle) {
                if (!this.isFun(handle)) {
                    throw new Error('传入的不是一个函数')
                }
                handle(this._resolve.bind(this), this._reject.bind(this))
                this.status = PENDING; // 初始的状态
                this.value = undefined; // 成功的值
                this.rejectValue = undefined; // 失败的值
                this.successcbs = [];//注册成功的回调
                this.errorcbs = [];// 注册失败的回调
            }
            _resolve(value) {
                if (this.status === PENDING) {
                    this.status = FULFILLED
                    this.value = value
                    this.successcbs.forEach(fn => { fn(this.value) })
                }
            }
            _reject(value) {
                if (this.status === PENDING) {
                    this.status = REJECTED
                    this.rejectValue = value
                    this.errorcbs.forEach(fn => { fn(this.rejectValue) })
                }
            }
            then(successFun, errorFun) {
                if (!this.isFun(successFun) || !this.isFun(errorFun)) {
                    throw new Error('处理函数不是函数')
                }
                // then的时候成功已完成，则直接调用成功回调
                if (this.status === FULFILLED) {
                    successFun(this.value)
                    return
                }
                // then的时候失败，则直接调用失败回调
                if (this.status === REJECTED) {
                    errorFun(this.rejectValue)
                    return
                }
                // then的时候正在pedding中，则等待
                if (this.status === PENDING) {
                    this.successcbs.push(successFun)
                    this.errorcbs.push(errorFun)
                }
            }
            isFun(fun) {
                return fun instanceof Function ? true : false
            }
        }
        let P = new MyPromise(function (res, rej) {
            setTimeout(() => {
                rej(123)
            }, 3000)
        })
        P.then(function (res) {
            console.log(res)
        }, function (rej) { console.log(rej) })
        console.log(P)
```
### Promise.all、Promise.race

```typescript
promise.all用来进行promise请求的并发操作，all方法接受的参数是一个由promise组成的数组，返回的结果也是promise最终的
```
### let var const

var声明提升，let不会声明提升（必须要先声明后使用）。

```typescript
console.log(num); // undefined
var num = 123

console.log(num2); // 报错
let num2 = 456
```
var会存在变量覆盖，let不会变量覆盖会报错。
var没有块级作用域，let会有自己的块级作用域。

const 创建必赋值，不可修改，一般用于定义常量。

## 手写

### 防抖

```typescript
function debounce(fn,wait){
  let timer = null;
  return args =>{
    if(timer){
      clearTimeout(timer)
    }
    timer = setTimeout(fn,wait)
  }
}
```
### 节流

```typescript
function thrrlo(fn,wait){
  let timer = null;
  return function(){
    if(!timer){
      timer = setTimeout(()=>{
        fn();
        timer = null;
      },wait)
    }
  }
}
```

### 深拷贝

```typescript
 //浅拷贝
        function clone1(obj) {
            let topicObj = {}
            for (let key in obj) {
                topicObj[key] = obj[key]
            }
            return topicObj
        }
        // 基础版深拷贝，没有考虑循环引用，数组，类型
        function clone2(obj) {
            if (obj instanceof Object) {
                let tempObj = {}
                for (let key in obj) {
                    tempObj[key] = clone2(obj[key])
                }
                return tempObj
            } else {
                return obj
            }
        }
        // 加强版考虑数组
        function clone3(obj) {
            if (typeof (obj) === 'object') {
                let tempObj = Array.isArray(obj) ? [] : {}
                for (let key in obj) {
                    tempObj[key] = clone3(obj[key])
                }
                return tempObj
            } else {
                return obj
            }
        }
        // 解决循环引用
        // 解决循环引用问题，我们可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题。
        // 这个存储空间，需要可以存储key-value形式的数据，且key可以是一个引用类型，我们可以选择Map这种数据结构：
        // 1检查map中有无克隆过的对象
        // 2有 - 直接返回
        // 3没有 - 将当前对象作为key，克隆对象作为value进行存储
        // 4继续克隆
        function clone4(obj, map = new Map()) {
            if (typeof obj === 'object') {
                let tempObj = Array.isArray(obj) ? [] : {};
                if (map.get(obj)) {
                    return map.get(obj)
                } else {
                    map.set(obj, tempObj)
                }
                for (let key in obj) {
                    tempObj[key] = clone4(obj[key], map)
                }
                return tempObj
            }
            return obj
        }

        let AA = {
            name: 'AA',
            age: 123,
            obj: {
                name: 'BB',
                age: 234
            },
            arr: [1, 2, 3]
        }
        AA.AA = AA // 循环引用示例
        console.log(AA, clone2(AA))
```
### 判断类型

```typescript
function isType(obj){
            return Object.prototype.toString.call(obj).slice(8,-1)
        }
// typeof   判断除了object、array、null之外的类型。这三个类型返回的都是'object'
// object 跟array可以用Array.isArray()来进行判断
// instanceof用来判断某个对象是否是某个构造函数的实例，注意左侧必须是对象
```
### call、apply、bind

```typescript
Function.prototype.myCall = function(context){
  context = Object(context) || window;
  // 此时获取到的this就是调用mycall的那个函数
  // context.fn = this;
  // 优化
  let key = new Symbol();
  context[key] = this;
  let args = [...arguments].slice(1); // 删除第一个元素，但不改变原数组
  let res = context.fn(args); // 绑定this，原理是fn的内部的this会指向调用fn的那个对象，也就是context
  // delete context.fn; // 删除多余的属性
  delete context.key; // 删除多余的属性
  return res
}
Function.prototype.myapply = function(context，args=[]){
  context = Object(context) || window;
  // 此时获取到的this就是调用mycall的那个函数
  // context.fn = this;
  // 优化
  let key = new Symbol();
  context[key] = this;
   const result = context[key](...args)
  delete context.key; // 删除多余的属性
  return res
}
```
### new

```typescript
function newFun(){
    let obj = Object.create(null) //创建一个没有原型链的对象
    const Constructor = [].shift.call(arguments) //传入的第一个参数就是构造函数 //获取构造函数 shift() 方法用于把数组的第一个元素从其中删除，并返回第一个元素的值。
    obj._proto_ = Constructor.prototype // 构造原型链。对象的原型指向构造函数的原型对象
    Constructor.apply(obj,arguments) //绑定this、以及参数（执行这个函数）
    return obj
}
```
### instanceof

```typescript
function instanceof(obj,constructorFun){
  let leftValue = obj._proto_;
  let rightValue = constructorFun.prototype;
  while(true){
    if(leftValue==null){
      return false
    }
    if(leftValue == rightValue){
      return true
    }
    leftValue = leftValue._proto_
  }
}
```
## 其他

## 待进一步学习

深拷贝最终解决方案，继承的最终解决方案，手写响应式原理，diff算法掌握


