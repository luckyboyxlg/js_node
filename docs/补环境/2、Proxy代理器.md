# Proxy代理

## 一、浏览器和Node环境区别

### 1、浏览器

页面渲染功能

+ 加载和控制页面元素的能力 ? 在js中由dom对象来完成.
+ 渲染引擎. -> 和我们基本上无关
+ 浏览器本身的一些东西(窗口大小, 浏览器信息) 、BOM对象
+ 能够执行js的能力 v8引擎... 负责执行js代码.



### 2、Node

包含了V8引擎、还拥有一些node做后端独有的一些功能. 

+ node里面是没有页面渲染的部分的. documentnode里面也没有BOM对象的东西. 

window补环境的逻辑: 

+ 自己创造的一个山寨的浏览器...
+ 在node环境中, 想办法补充浏览器(document, window)的环境.补环境的时候, 要注意, node环境中独有的内容.要想办法剔除掉.
+ 原型链.的处理继承的方案:Object.setPrototypeOf(子.prototype, 父.prototype);
+ 补环境最终的结局:可以直接把它的js代码全抠下来. 直接能运行. 直接出结果.  最理想的状态是一行代码都不改



## 二、了解Proxy

### 1、Proxy的概念和作用

Proxy是ES6中新增的一个功能，它可以在某个对象前架设一个“拦截器”，从而可以对该对象的访问进行拦截和控制。可以理解为是对对象访问的一个代理，通过代理可以改变对象的默认行为。

Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

### 2、Proxy的作用主要有以下几个方面：

1. 对象的拦截和控制：可以对对象的属性访问、赋值、函数调用等操作进行拦截和控制，从而实现对对象行为的定制。

2. 数据劫持：可以通过Proxy实现数据双向绑定、深度监听、表单校验等数据劫持操作。

3. 权限控制：可以使用Proxy实现对象属性的访问权限控制，限制一些敏感属性的访问。

4. 性能优化：可以使用Proxy进行缓存、懒加载和单例模式等性能优化操作。

### 3、Proxy和Object.defineProperty的区别

Proxy和Object.defineProperty都可以用于拦截和控制对象的属性访问，但是它们之间有以下几个区别：

1. Proxy支持拦截更多的操作：Proxy可以拦截更多的对象操作，包括对象属性的读取和设置、函数调用、in操作符、for...in循环等等，而Object.defineProperty只能拦截属性的访问和设置。

2. Proxy是基于对象的拦截：Proxy是基于对象的拦截，即一个Proxy实例对应一个被拦截的对象，通过代理可以改变整个对象的行为。而Object.defineProperty是基于属性的拦截，可以对单个属性进行拦截。

3. Proxy具有别名效应：在不需要拦截的情况下，可以直接使用对象的别名和引用来访问对象。而Object.defineProperty修改对象行为之后，不可以直接使用对象的别名和引用来访问属性。

4. Proxy可以使用Reflect对象：Proxy通过Reflect对象来执行默认操作，而Object.defineProperty则不能。

### 4、Proxy优势和劣势

Proxy作为ES6中新增的一个功能，具有以下优势和劣势：

优势：

1. 更加灵活和强大：Proxy比Object.defineProperty更加灵活和强大，可以拦截并控制更多的对象操作，包括读取和设置属性、函数调用等等。

2. 可以动态修改对象行为：通过修改Proxy的拦截函数，可以动态地改变对象的行为，而Object.defineProperty不能实现这样的功能。

3. 容易扩展：当需要添加新的拦截函数时，可以通过添加新的Proxy拦截器来实现，而不需要对代码进行大规模的修改，开发起来更加容易。

劣势：

1. 兼容性不足：虽然现在很多主流浏览器都支持Proxy，但是一些旧版的浏览器还不支持，因此在实际使用中还需要考虑兼容性的问题。

2. 性能问题：相比Object.defineProperty等原生API，使用Proxy会带来一些额外的性能开销，尤其是在递归拦截、大量拦截操作等复杂场景下，会对程序的性能造成一定影响。但是在大多数场景下，Proxy的性能问题可以被忽略不计。



## 三、 基本语法与API

Proxy的基本语法和API包括以下几个方面：

### 1、基本语法：

```javascript
let target = {}; // 被拦截的对象
let handler = {}; // 对象的拦截器
let proxy = new Proxy(target, handler); // 创建Proxy实例
```

上述代码中，通过创建Proxy实例来拦截target对象的访问。handler是一个拦截器对象，代表对target对象进行拦截和处理。Proxy拦截器需要实现get、set、apply等方法。

### 2、Proxy的基本使用

创建一个对象

```javascript
const obj = {
    name:'lucky',
    age:18
}
```

再创建Proxy代理对象

```javascript
const objProxy = new Proxy(obj,{})
```

参数一：需要代理的对象  

参数二：捕获器，对代理对象的属性进行访问、赋值等操作的时候触发，与Object.defineproperty的存取描述符类似。如果为空对象，就只有set、get这两个默认捕获器，并且不会有过多的操作，get捕获器就直接返回访问属性的值，set捕获器就将新的值赋值给访问属性。

```javascript
console.log(objProxy.name)   //'lucky'
console.log(objProxy.age)    //18
 
objProxy.name = '迪丽热巴'
objProxy.age = 20
 
//对代理对象操作后，代理对象就会对原对象进行操作
console.log(obj.name)       //'迪丽热巴'
console.log(obj.age)        //20
```

注意：Proxy只能代理对象（Object、Function、Array），非对象值无法进行代理。



### 3、Proxy的13种捕获器：

+ handler.get()：属性读取操作的捕捉器。
+ handler.set()：属性设置操作的捕捉器。
+ handler.has()：in 操作符的捕捉器。
+ handler.deleteProperty()：delete 操作符的捕捉器。
+ handler.getPrototypeOf()：Object.getPrototypeOf 方法的捕捉器。
+ handler.setPrototypeOf()：Object.setPrototypeOf 方法的捕捉器。
+ handler.isExtensible()：Object.isExtensible 方法的捕捉器。
+ handler.preventExtensions()：Object.preventExtensions 方法的捕捉器。
+ handler.getOwnPropertyDescriptor()：Object.getOwnPropertyDescriptor 方法的捕捉器。
+ handler.defineProperty()：Object.defineProperty 方法的捕捉器。
+ handler.ownKeys()：Object.getOwnPropertyNames 方法和Object.getOwnPropertySymbols 方法的捕捉器。
+ handler.apply()：函数调用操作的捕捉器。（函数也是一个对象，这里就对函数调用时进行监听
+ handler.construct()：new 操作符的捕捉器。（函数执行new操作符的时候进行监听）

**总结：**

+ 对于我们逆向来说最重要的就是get，与set，当逆向的时候代码中通过会有或者和设置的操作，如果我们当前node环境中不存在，而调用的位置又存在异常处理，那么最终会导致结果不正确从而导致逆向失败

- 使用Proxy创建的代理对象进行操作的好处是，可以不用直接通过Object.defineproperty去操作原对象，通过Object.defineproperty直接操作原对象就会将对象原本的数据属性描述符变成了访问属性描述符
- Object.defineproperty(我们在hook那里使用过)



### 4、常用API

(1) get：拦截对象属性的读取操作。

```javascript
let target = {a: 1};
let handler = {
    get(target, property, receiver) {
        console.log(`getting ${property}`);
        return target[property];
    }
};
let proxy = new Proxy(target, handler);
console.log(proxy.a); // 输出getting a 和 1
```

(2) set：拦截对象属性的赋值操作。

```javascript
let target = {a: 1};
let handler = {
    set(target, property, value, receiver) {
        console.log(`setting ${property} = ${value}`);
        target[property] = value;
        return true;
    }
};
let proxy = new Proxy(target, handler);
proxy.a = 2; // 输出setting a = 2
```

(3) apply：拦截函数的调用操作。

```javascript
let target = function (a, b) {
    return a + b;
}
let handler = {
    apply(target, thisArg, args) {
        console.log(`calling ${target.name}`);
        return target(...args);
    }
};
let proxy = new Proxy(target, handler);
console.log(proxy(1, 2)); // 输出calling proxy 和 3
```

(4) has：拦截in操作符的操作。

```javascript
let target = {a: 1};
let handler = {
    has(target, property) {
        console.log(`checking ${property} in target`);
        return property in target;
    }
};
let proxy = new Proxy(target, handler);
console.log('a' in proxy); // 输出checking a in target 和 true
```

(5) ownKeys：拦截Object.getOwnPropertyNames和Object.getOwnPropertySymbols操作。

```javascript
let target = {a: 1};
let handler = {
    ownKeys(target) {
        console.log(`getting ownKeys`);
        return ['b', ...Object.getOwnPropertyNames(target)];
    }
};
let proxy = new Proxy(target, handler);
console.log(Object.keys(proxy)); // 输出getting ownKeys 和 ['b', 'a']
```

(6) Reflect：Reflect对象提供了Proxy内置拦截函数的基础实现，可以通过Reflect对象执行默认操作。

```javascript
let target = {a: 1};
let handler = {
    ownKeys(target) {
        console.log(`getting ownKeys`);
        return ['b', ...Object.getOwnPropertyNames(target)];
        // return ['b', ...Reflect.ownKeys(target)];
    }
};
let proxy = new Proxy(target, handler);
console.log(Object.keys(proxy)); // 输出getting ownKeys 和 ['b', 'a']
```



### 5、Proxy捕获器的使用-组合

**Proxy捕获器用来对代理对象属性进行访问、赋值等操作时的一个捕获。与Object.defineproperty的存取描述符类似。下面我们就认识一下常用的四个捕获器的基本使用。**

JavaScript代码：

```javascript
const objProxy = new Proxy(obj,{
    //get操作符
    get:function(target,key){
        console.log(`监听到访问${key}属性`,target)
        return target[key]    //返回访问属性的值
    },
    //set操作符
    set:function(target,key,newValue){
        console.log(`监听到给${key}属性设置值`,target)
        tarset[key] = newValue    //将属性最新值，赋值给代理对象属性
    },
    //has操作符
    has:function(target,key){
        console.log(`监听到使用in操作符${key}`,target)
        return key in target
    },
    //delete操作符
    deleteProperty:function(){
        console.log(`监听到使用delete操作符${key}`,target)
        delete target[key]    
    }
})
 
console.log(objProxy.name)
console.log(objProxy.age)
 
objProxy.name = 'wx'
objProxy.age = 20
 
//in操作符
console.log('name' in objProxy)  //true
//delete操作符
delete objProxy.name
```



## 四、Reflect

### 1、Reflect的认识

Reflect是一个对象，字面意思是"反射"。

### 2、Reflect有什么用呐？

它主要提供了很多操作JavaScript对象的方法，有点像Object中操作对象的方法； 比如Reflect.getPrototypeOf(target)类似于 Object.getPrototypeOf()；

**如果我们有Object可以做这些操作，那么为什么还需要有Reflect这样的新增对象呢？**

这是因为在早期的ECMA规范中没有考虑到这种对 对象本身 的操作如何设计会更加规范，所以将这些API放到了Object上面；但是Object作为一个构造函数，这些操作实际上放到它身上并不合适；另外还包含一些类似于 in、delete操作符，让JS看起来是会有一些奇怪的；所以在ES6中新增了Reflect，让我们这些操作都集中到了Reflect对象上。

### 3、 Reflect基本使用

我们对上面的代码做一下修改，使用Reflect代替一下

```javascript
const objProxy = new Proxy(obj,{
    get:function(target,key){
        console.log(`监听到访问${key}属性`,target)
        return Reflect.get(target,key)    //改为Reflect.get
    },
    set:function(target,key,newValue){
        console.log(`监听到给${key}属性设置值`,target)
        Reflect.set(target,key,newValue)    //改为Reflect.set
    }
})
 
console.log(objProxy.name)
 
objProxy.name = 'wx'
```

示例

```javascript
const istrue = Reflect.set(target,key,newValue)
const result = istrue?'设置成功':"设置失败"
```



## 五、逆向完整使用

代码

```javascript
function myProxy(obj,name){
    return new Proxy(obj,{
        get(target, propKey, receiver){ //拦截对象属性的读取，比如proxy.foo和proxy['foo']。
            let temp = Reflect.get(target,propKey,receiver);
            console.log(`${name} -> get ${propKey.toString()} return -> ${temp}`);
            if(typeof temp == 'object') {
                temp = myProxy(temp,name + " => " + propKey)
            }
            return temp;
        }, 
        set(target, propKey, value, receiver){ //拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。
            const temp = Reflect.set(target,propKey,value,receiver);
            console.log(`${name} -> set ${propKey} value -> ${value}`);
            return temp;
        }, 
        has(target, propKey){ //拦截propKey in proxy的操作，返回一个布尔值。
            const temp = Reflect.has(target,propKey);
            console.log(`${name} -> has ${propKey}`);
            return temp;
        }, 
        deleteProperty(target, propKey){ //拦截delete proxy[propKey]的操作，返回一个布尔值。
            const temp = Reflect.deleteProperty(target,propKey);
            return temp;
        }, 
        ownKeys(target){ //拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。
            const temp = Reflect.ownKeys(target);
            return temp;
        }, 
        getOwnPropertyDescriptor(target, propKey){ //拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
            const temp = Reflect.getOwnPropertyDescriptor(target,propKey);
            return temp;
        }, 
        defineProperty(target, propKey, propDesc){ //拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。
            const temp = Reflect.defineProperty(target,propKey,propDesc);
            return temp;
        }, 
        preventExtensions(target){ //拦截Object.preventExtensions(proxy)，返回一个布尔值。
            const temp = Reflect.preventExtensions(target);
            return temp;
        }, 
        getPrototypeOf(target){ //拦截Object.getPrototypeOf(proxy)，返回一个对象。
            const temp = Reflect.getPrototypeOf(target);
            return temp;
        }, 
        isExtensible(target){ //拦截Object.isExtensible(proxy)，返回一个布尔值。
            const temp = Reflect.isExtensible(target);
            return temp;
        }, 
        setPrototypeOf(target, proto){ //拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
            const temp = Reflect.setPrototypeOf(target,proto);
            return temp;
        }, 
        apply(target, object, args){ //拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
            const temp = Reflect.apply(target, object, args);
            return temp;
        }, 
        construct(target, args){ //拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。
            const temp = Reflect.construct(target, args);
            return temp;
        } 
    })
}

window = new myProxy(global,"window"); //代理器代理之后，会创建一个新对象

this == global; //global就是全局上下文代理对象；

this != window; // this 是global 但是 window是代理器，所以这里是 !=;

let myWindow = this;

let abc = {
    ac: function(){
        this == abc // 在对象中，调用函数方法，进入函数局部作用域后，this指向父对象（abc）
    }
}


const window = myProxy(globalThis,"window");
```

代理器失效原因

1.没有访问属性操作

2.没有使用代理对象