---
title: Javascript入门基础
tags:
- Javascript
categories:
- 前端
date: 2016-11-10 10:19:51
---

## 数据类型

### 类型
* object(包含Function，Array，Date等)  
* number  
* string  
* boolean  
* null  
* undefined

### 检测
typeof 基本类型，function， null可以用===判断
```javascript
if (typeof 100) {}              // "number"   
if (typeof true) {}             // "boolean"    
if (typeof Function()) {}       // "function"    
if (typeof undefined) {}        // "undefined"  
if (typeof Object()) {}         // "object"  
if (typeof [1, 2])              // "object"  
if (typeof NaN) {}              // "number"  
if (typeof null) {}             // "object"  
```

instanceof
```javascript
// instanceof  左边是对象（如果不是，直接返回false），右边是构造器（如果不是，直接报错），判断构造器的prototype属性是否在对象的原型链上
console.log([1, 2] instanceof Array);  // true
console.log(1 instanceof Array);  // false
console.log(Array.prototype);  // []
console.log({} instanceof Array);  //false
console.log(Object.prototype);  // {}
console.log({} instanceof Object);  // true
console.log([] instanceof Object);  // true
```
    
Object.prototype.toString

```javascript
if (Object.prototype.toString.apply([1, 2, 3]) === "[object Array]")  
if (Object.prototype.toString.apply(function(){}) === "[object Function]")  
if (Object.prototype.toString.apply(null) === "[object Null]")  // ie678返回[object Object]  
if (Object.prototype.toString.apply(undefined) === "[object Undefined]")  
if (Object.prototype.toString.apply(12313) === "[object Number]")  
if (Object.prototype.toString.apply('12313') === "[object String]")
```

## 表达式

表达式是一种js短语，可使js解释器用来产生一个值
```javascript
// 原始表达式
// 常量
x = 3.14;
// 直接量
y = "test"; 
// 关键字 
x = null, y = this,  z = true;
// x,y,z 为变量
// 复合表达式 原始表达式+运算符+原始表达式
x = 10 * 20;
// 初始化表达式
x = [1, 2];
y = {x: 1, y: 2};
    
// 函数表达式
let f1 = function(){};
// let f2 = (function(){console.log('1111');}){};
    
// 属性访问表达式
o = {x:1};
p = o.x;
q = o['x'];
    
// 调用表达式
f1();
    
// 对象创建表达式
f3 = new Func(1, 2);
f4 = new Object();
```

## 运算符

* 一元（+num）  
* 二元（a + b）  
* 三元（c：a：b） 赋值(x += 1)  
* 比较(a == b)  
* 算术（a - b）  
* 位 （a | b）  
* 逻辑 (exp1 && exp2)  
* 字符串("a" + "b")  
* 特殊

### 特殊运算符

* c? a:b  
* var val = (1, 2, 3) // 逗号运算符  
* delete obj.x // 删除对象属性  
* 'x' in window; // in 运算符  
* instanceof, typeof // 判断类型  
* new Foo(); // new 运算符  
* this // this运算符  
* void 0 // undefined

## 语句

块语句block  
块语句是没有作用域的
```javascript
{  
  let str = 'hi';  
  console.log(str);  
}  
```

声明语句 var  
var a=1, b=1; 函数语句function
```javascript
// 函数声明语句  
function fd() {
  return true;  
}    
// 函数表达式  
let fe = function() {
    //do sth
};
```

for in 语句
```javascript
let p;  
let obj = {x:1, y:2};  
for(p in obj) {
// 顺序不确定  
}
```

swith语句
```javascript
let val = 2;
switch (val) {
case 1:
    console.log(1);
    break;
case 2:
    console.log(2);
    break;
case 3:
    console.log(3);
    break;
default:
    console.log(0);
    break;
}
```

循环语句
```javascript
while (isTrue) {
    // do sth
}
do {
    // do sth
} while (isTrue);

let i;
for (i = 0; i < n; i++) {
    // do sth
}
```
  
with语句 // 不使用 try catch语句
```javascript
try {
    try {
        throw new Error('oops');
    } catch(ex) {
        console.error('inner catch', ex.message);
        throw ex;
    } finally {
        console.log('inner  finally');
    }
} catch(ex) {
    console.error('outer catch', ex.message);
} finally {
    console.log('outer finally');
}
```  

### 严格模式(待补充）
```javascript
function func() {
    'use strict';
}
```

## 对象

对象创建  
字面量创建
```javascript
var obj = {x: 1};
console.log(obj);
console.log(obj.y);
Object.prototype.y = 2;
console.log(obj);
console.log(obj.y);
obj.y = undefined;
console.log(obj);
console.log(obj.y);
```
```shell
{ x: 1 }
undefined
{ x: 1 }
2
{ x: 1, y: undefined }
undefined
```
    
new构造器创建
```javascript
function Foo() { this.color = 'red';}
Foo.prototype.background = '#000';
var redColor = new Foo();
console.log(redColor);
console.log(typeof redColor.toString);
console.log(redColor.color);
console.log(redColor.background);
console.log(foo);
console.log(Foo.prototype);
console.log(Foo.prototype.prototype);
console.log('background' in redColor);
redColor.background = '#FFF';
console.log(redColor.background);
console.log(Foo.prototype.background);
```
```textmate
Foo { color: 'red' }
function
red
#000
[Function: Foo]
Foo { background: '#000' }
undefined
true
#FFF
#000
```

Object.create()创建
```javascript
function Obj() {
    this.x = 1;
}
console.log(Obj);
let obj1 = new Obj();
console.log(obj1);
let obj2 = Object.create(Obj);
console.log(obj2);
console.log(obj2.x);
let obj3 = Object.create(obj1);
console.log(obj3);
console.log(obj3.x);
```
```shell
[Function: Obj]
Obj { x: 1 }
Function {}
undefined
Obj {}
1
```

### 对象原型链
 
prototype是函数对象上的预设的对象属性， 原型是对象上的原型，通常都是构造器的prototype属性  
```javascript
function Abc() {
     this.y = 2;
     var z = 3;
 }
 console.log(Abc);  // [Function: Abc]
 console.log(Abc.prototype);  // Abc {}
 console.log(typeof Abc.prototype);  // object
 
 Abc.prototype.x = 1;
 console.log(Abc.prototype);  // Abc { x: 1 }
 
 var obj1 = new Abc();
 console.log(obj1.__proto__);  // Abc { x: 1 }
 console.log(obj1.__proto__ === Abc.prototype);  // true  new构造器创建的对象的原型是构造器的prototype属性
 console.log(obj1);  // Abc { y: 2 }
 console.log(obj1.y);  // 2
 console.log(obj1.x);  // 1
``` 
     
> 所有构造器/函数的__proto__都指向Function.prototype，它是一个空函数（Empty function）
```javascript
console.log(Number.__proto__ === Function.prototype);  // true  
console.log(Boolean.__proto__ === Function.prototype); // true  
console.log(String.__proto__ === Function.prototype);  // true  
console.log(Object.__proto__ === Function.prototype);  // true  
console.log(Function.__proto__ === Function.prototype); // true  
console.log(Array.__proto__ === Function.prototype);   // true  
console.log(RegExp.__proto__ === Function.prototype);  // true  
console.log(Error.__proto__ === Function.prototype);   // true  
console.log(Date.__proto__ === Function.prototype);    // true  
console.log(Math.__proto__ === Object.prototype);  // true  
console.log(JSON.__proto__ === Object.prototype);  // true 
// 函数声明
function Person() {}
// 函数表达式
let Man = function() {};
console.log(Person.__proto__ === Function.prototype); // true  
console.log(Man.__proto__ === Function.prototype)    // true  
```
   
> 所有的构造器都来自于Function.prototype，甚至包括根构造器Object及Function自身。所有构造器都继承了Function.prototype的属性及方法。如length、call、apply、bind（ES5）
```javascript
console.log(typeof Function.prototype); // function
console.log(typeof Object.prototype);   // object
console.log(typeof Number.prototype);   // object
console.log(typeof Boolean.prototype);  // object
console.log(typeof String.prototype);   // object
console.log(typeof Array.prototype);    // object
console.log(typeof RegExp.prototype);   // object
console.log(typeof Error.prototype);    // object
console.log(typeof Date.prototype);     // object
console.log(typeof Object.prototype);   // object  
console.log(Function.prototype.__proto__ === Object.prototype); // true    
console.log(Object.prototype.__proto__ === null)  // true  
```

> 所有对象的__proto__都指向其构造器的prototype  

JavaScript引擎内置构造器
```javascript
var obj = {name: 'jack'};
var arr = [1,2,3];
var reg = /hello/g;
var date = new Date;
var err = new Error('exception');

console.log(obj.__proto__ === Object.prototype); // true
console.log(arr.__proto__ === Array.prototype);  // true
console.log(reg.__proto__ === RegExp.prototype); // true
console.log(date.__proto__ === Date.prototype);  // true
console.log(err.__proto__ === Error.prototype);  // true
```

自定义的构造器
```javascript
function Person(name) {
    this.name = name
}
let p = new Person('jack');
console.log(p.__proto__ === Person.prototype); // true
```
   
每个对象都有一个constructor属性，可以获取它的构造器
```javascript
function Person(name) {
    this.name = name
}
let p = new Person('jack');
console.log(p.__proto__ === p.constructor.prototype) // true
```

### 对象属性操作

属性读写
```javascript
// 属性读写
let obj1 = {x1: 1, x2: 2};
let i = 1, n = 2;
for(; i<= n; i++) {
    console.log(obj1['x' + i]);
}
// 输出1，2
let p;
for(p in obj1) {
    console.log(obj1[p]);
}
// 输出1，2
let obj2 = {x: 1};
// 读写异常
console.log(obj2.y); // undefined
let yz = obj2.y.z;  // TypeError: Cannot read property 'z' of undefined
obj2.y.z = 2;      //TypeError: Cannot set property 'z' of undefined
// 正确方式
let yz;
if(obj2.y) {
    yz = obj2.y.z;
}
// 或者
let yz = obj2 && obj2.y && obj2.y.z  // 这个会返回undefined
```

属性删除
```javascript
let person = {age: 28, title: 'fe'};
console.log(delete person.age);  // true
console.log(delete person['age']);  // true
console.log(person.age);  // undefined
console.log(delete person.age);   // 注意这个也返回true 判断操作结束后该属性是否存在

// 有些属性是不能删除的
console.log(delete Object.prototype);  // false

// 原因
var descriptor = Object.getOwnPropertyDescriptor(Object, 'prototype');
console.log(descriptor.configurable);  // false

// 变量及函数不可删除
let globalVal = 1;
console.log(delete globalVal);  // false

(function() {
    var localVal = 1;
    console.log(delete localVal);
}());  // false

function fd() {}
console.log(delete fd);  // false

(function() {
    function fd() {}
    console.log(delete fd)
}());  // false

// 隐示定义的变量可以
ohNo = 1;
console.log(delete ohNo);  // true
```
    
对象属性检测
```javascript
var cat = new Object();
cat.legs = 4;
cat.name = 'Kitty';

console.log('legs' in cat);  // true
console.log('abc' in cat);  // false
console.log('toString' in cat);  // true 继承属性

console.log(cat.hasOwnProperty('legs'));  // true
console.log(cat.hasOwnProperty('toString'));  // false

console.log(cat.propertyIsEnumerable('legs'));  // true
console.log(cat.propertyIsEnumerable('toString'));  // false


Object.defineProperty(cat, 'price', {enumerable: false, value: 1000});
// defineProperty是Object的方法，默认的标签enumerable值为false
// Object.x创建属性，默认的标签值为true
console.log(cat.propertyIsEnumerable('price'));  // false
console.log(cat.hasOwnProperty('price'));  // true


// 判断属性是否存在
if(cat && cat.legs) {
    cat.legs *= 2;
}

if(cat.legs !== undefined) {
    // !== undefined 或者 !== null
}
if(cat.legs !== undefined) {
    // !== undefined
}
```
    
对象属性枚举
```javascript
let o = {x: 1, y: 2, z: 3};
console.log('toString' in o);  // true
o.propertyIsEnumerable('toString');  // false
let k;
console.log(o.x);  // 1
console.log(o.y);  // 2
console.log(o.z);  // 3
for(key in o) {
    console.log(key);  // x, y, z
    console.log(o.key);  // undefined
    console.log(o[key]);  // 1, 2, 3
}

let obj = Object.create(o);
obj.a = 4;
let k2;
for(k in obj) {
    console.log(k); // a, x, y, z
}
let key1;
for (key1 in obj) {
    if(obj.hasOwnProperty(key1)) {
        console.log(key1); // a
    }
}
```

对象属性的get/set方法
```javascript
// 对象属性的get/set方法
let man = {
    name: 'Bug',
    weibo: 'Bug',
    get age() {
        return new Date().getFullYear() - 1988;
    },
    set age(val) {
        console.log('cannot be set ' + val);
    },
};

console.log(man.age);  // 调用age的get方法27
man.age = 100;  // 调用age的set方法 cannot be set 100
console.log(man.age);  // 27


let Man = {
    weibo: 'Bug',
    $age: null,
    get age() {
        if(this.$age === undefined) {
            return new Date().getFullYear() - 1988;
        } else {
            return this.$age;
        }
    },
    set age(val) {
        val = +val; // 转为数字
        if(!isNaN(val) && val > 0 && val < 150) {
            this.$age = + val;
        } else {
            throw new Error('Incorrect val = ' + val );
        }
    }
};

console.log(Man.age);  // 27
Man.age = 100;
console.log(Man.age);  // 100
//man.age = 'abc';  // Error: Incorrect val = NaN

// get/set方法与原型链

// 这是错误的定义对象属性get方法方式
function Foo1() {}
Foo1.prototype.z = {
    get: function() {
        return 2;
    }
};
let a1 = new Foo1();
console.log(a1.z);  // 这个会被认为是对象 { get: [Function] }
a1.z = 200;
console.log(a1.z);  // 200

// 正确定义方法
function Foo() {}
Object.defineProperty(Foo.prototype, 'z', {
    get: function() {
        return 1;
    },
    // configurable: true,  // 开启此属于也不会被改变
    // writable: true,  // 此项不能配置writable/value get/set 二选一
 });
let a2 = new Foo();
console.log(a2.z);   // 1
a2.z = 100;
console.log(a2.z);  // 但是还是1 并没有被改变

Object.defineProperty(a2, 'z', {
    value: 100,
    configurable: true
});
console.log(a2.z);  // 100
delete a2.z;
console.log(a2.z);  // 返回到1

// 另外一个例子
let o = {};
Object.defineProperty(o, 'x', {
    value: 1
});  // 默认writable: false, configurable: false
let obj = Object.create(o);
console.log(obj.x);  // 1
obj.x = 100;
console.log(obj.x);  // 还是1


Object.defineProperty(obj, 'x', {
    writable: true,
    configurable: true,
    value: 100
});
console.log(obj.x);  // 100
obj.x = 200;
console.log(obj.x); // 200
```
    
    
### 属性标签
```javascript
person = {};
Object.defineProperty(person, 'type', {
    configurable: false,
    writable: true,
    enumerable: false,
    value: 'Object',
});

console.log(Object.keys(person));   // [] 返回空数组，不能枚举
console.log(Object.getOwnPropertyDescriptor(person, 'type'));   // { value: 'Object', writable: true, enumerable: false, configurable: false }


// writable为true，可以通过赋值方法修改value值 [通过赋值修改属性值只和writable标签有关，和configurable无关]
person.type = 'Object2';
console.log(Object.getOwnPropertyDescriptor(person, 'type'));  // { value: 'Object2', writable: true, enumerable: false, configurable: false }

// configurable: false 无法使用delete删除属性， 无法使用get/set方法，  无法重新配置属性标签（除了将writable从true修改为false）
console.log(delete person.type);   // 返回false

// 只有writable和configurable标签都为false时，不能修改value标签值，其他情况都可以修改value标签值
Object.defineProperty(person, 'type', {
    configurable: false,
    writable: true,
    enumerable: false,
    value: 'Object1',
});
console.log(Object.getOwnPropertyDescriptor(person, 'type'));   // { value: 'Object1', writable: true, enumerable: false, configurable: false }

// 可以重新配置writable标签值(只能重true改为false)
Object.defineProperty(person, 'type', {
    writable: false,
});
console.log(Object.getOwnPropertyDescriptor(person, 'type'));   // { value: 'Object1', writable: false, enumerable: false, configurable: false }

// 无法将writable从false改为true
Object.defineProperty(person, 'type', {
    writable: true,
});
console.log(Object.getOwnPropertyDescriptor(person, 'type'));   // TypeError: Cannot redefine property: type

// 无法重新配置enumerable，configurable属性
Object.defineProperty(person, 'type', {
    enumerable: true,
});
console.log(Object.getOwnPropertyDescriptor(person, 'type'));   // TypeError: Cannot redefine property: type
```
    

### 对象标签

原型标签__proto__
```javascript
function Person(name) {
    this.name = name;
}
let p = new Person('jack');
console.log(p.__proto__ === Person.prototype);  // true
```

类型标签
```javascript
//  对象class标签，表示对象是哪个类型，没有直接的方法去修改或者获取
let toString = Object.prototype.toString;
console.log(toString.call(null));  // [object Null]
console.log(toString.call(undefined));  // [object Undefined]
console.log(toString.call(1));  // [object Number]
console.log(toString.call(Number(1)));  // [object Number]
console.log(toString.call(true));  // [object Boolean]
console.log(toString.call(Boolean(true)));  // [object Boolean]
```

可扩展标签
```javascript
 // extensible 可扩展标签，表示是否可以继续添加对象属性
let obj = {x: 1, y: 2};
console.log(Object.isExtensible(obj));  // true 对象可扩展
Object.preventExtensions(obj);  // 配置对象不可扩展
console.log(Object.isExtensible(obj));  // false 对象不可扩展
obj.z =1;
console.log(obj.z);  // undefined 添加属性失败
console.log(Object.getOwnPropertyDescriptor(obj, 'x'));  // // { value: 1, writable: true, enumerable: true, configurable: true }

// Object.seal方法，对象的所有属性configurable值为false
Object.seal(obj);
console.log(Object.getOwnPropertyDescriptor(obj, 'x'));  // { value: 1, writable: true, enumerable: true, configurable: false }
console.log(Object.isSealed(obj)); // true

// Object.freeze方法，对象的所有属性configurable，writable标签值为false
Object.freeze(obj);
console.log(Object.getOwnPropertyDescriptor(obj, 'x'));   // // { value: 1, writable: false, enumerable: true, configurable: false }
console.log(Object.isFrozen(obj));  // true

```

### Json对象
```javascript
// 将js对象转为json格式JSON.stringify()
let x = {x: 1, y: true, z: [1, 2, 3], nullVal: null};
console.log(JSON.stringify(x));  // {"x":1,"y":true,"z":[1,2,3],"nullVal":null}

// undefined,NaN,Infinity等特殊属性值
let y = {val: undefined, a: NaN, b: Infinity, c: new Date()};
console.log(JSON.stringify(y));  // {"a":null,"b":null,"c":"2015-12-19T12:30:40.456Z"}

// 将json格式转为js对象JSON.parse
let z = JSON.parse('{"x": 1}');
console.log(z.x);  // 1

//toJSON方法
let obj = {
    x: 1,
    y: 2,
    o: {
        o1: 1,
        o2: 2,
        toJSON: function() {
            return this.o1 + this.o2;
        }
    },
};
console.log(JSON.stringify(obj));  // {"x":1,"y":2,"o":3}
```

### toString valueOf方法
```javascript
// 对象在作为操作数时，解释器总是优先调用valueOf()--(Date类型的对象在二元“+”运算时例外),而其他情况，解释器总是认为我们想要的是字符串，所以会优先调用toString()。
let obj = {x: 1, y: 2};
console.log(obj.toString());  // [object Object]
console.log(+obj.toString());  // NaN
console.log(3 + obj.toString());  // 3[object Object] 会被理解为字符串拼接
console.log(+obj.valueOf());  // NaN

// 重写
obj.toString = function() {
    return this.x + this.y;
};
console.log(+obj);  // 3
obj.valueOf = function() {
    return this.x + this.y + 100;
};
console.log(+obj);  // 103

// 如果只重写了toString，对象转换时会无视valueOf的存在来进行转换。但是，如果只重写了valueOf方法，在要转换为字符串的时候会优先考虑valueOf方法。在不能调用toString的情况下，只能让valueOf上阵了。

var cc = {
    i: 10,
    valueOf: function() {
        console.log('valueOf');
        return this.i;
    }
};

console.log(cc);// 10 { [Number: 10] i: 10, valueOf: [Function] }
console.log(+cc); // 10 valueOf
console.log(''+cc); // 10 valueOf
console.log(String(cc)); // [object Object]
console.log(Number(cc)); // 10 valueOf
console.log(cc === '10'); // true valueOf
```
    
## 数组

### 增删改查
创建数组
```javascript
// 字面量创建数组 数组的长度不能超过2^23 - 1 = 4,294,967,295
let bat = ['B', 'A', 'T'];
let students = [{name: 'B', age: 27}, {name: 'A', age: 30}];
let arr1 = ['T', 123, true, null, undefined];
let arrInArr = [[1, 2], [1, 2, 3]];
    
// 对象创建数组 new关键字可以省略
let arr2 = [];  // var = [];
let arrWithLength = new Array(100);  // undefined * 100
let arrLikesLiteral = [true, false, null, 1, 2, 'hi'];  // [true, false, null, 1, 2, 'hi']

```
    
数组元素操作
```javascript
// 数组元素读写
// 索引访问
let x = ['a', 'b', 'c', 'd', 'e'];
console.log(x[1]);  // b
console.log(x.length);  // 5
x[5] = 'f';
console.log(x);  // [ 'a', 'b', 'c', 'd', 'e', 'f' ]
console.log(x.length);  // 6

// delete方法并不会改变数组长度,delete 和定义为undefined的区别
delete x[0];
console.log(x);  // [ , 'b', 'c', 'd', 'e', 'f' ]
console.log(x.length);  // 6
console.log(0 in x);  // 会返回false
x[1] = undefined;
console.log(x);  // [ , undefined, 'c', 'd', 'e', 'f' ]
console.log(x.length);  // 6
console.log(1 in x);  // true

// 数组是动态的，无需指定大小

// 指定索引位置，改变数组
let y = [];
y[1] = 'b';
console.log(y.length);  // 2
console.log(y);   // [ , 'b' ]

// push方法，在数组尾部添加元素,并返回新的数组长度
console.log(y.push('c'));  // 3
console.log(y);  // [ , 'b', 'c' ]

// 在数组尾部添加的另一种方法
y[y.length] = 'd';
console.log(y);  // [ , 'b', 'c', 'd' ]

// unshift方法，在数组头部添加元素，并返回新的数组长度
console.log(y.unshift('a'));  // 5
console.log(y);  // [ 'a', , 'b', 'c', 'd' ]

// pop方法，删除数组尾部的元素并返回这个元素
console.log(y.pop());  // 'd'
console.log(y);  // [ 'a', , 'b', 'c' ]

// 删除数组尾部元素的另个方法
y.length -= 1;
console.log(y);  // [ 'a', , 'b' ]

// shift方法，删除数组头部的元素并返回这个元素
console.log(y.shift());  // 'a'
console.log(y);  // [ , 'b' ]
```
    
数组迭代
```javascript
// 数组迭代
let x = ['a', 'b', 'c', 'd', 'e', 'f', 'g'];
for(let i = 0; i < x.length; i++) {
    console.log(x[i]);
}
// a b c d e f g

for(i in x) {
    console.log(x[i]);
}
// a b c d e f g

// for in 表达式的坑：会迭代原型的元素
Array.prototype.x = 'keng';
for(i in x) {
    console.log(x[i]);
}
// a b c d e f g keng

for(i in x) {
    if(x.hasOwnProperty(i)) {
        console.log(x[i]);
    }
}
// a b c d e f g

```
    
特殊数组：二维数组和稀疏数组
```javascript
// 特殊数组：二维数组，稀疏数组
// 二维数组
let arr = [['a', 'b'], ['c', 'd'], ['e', 'f']];
let i = 0, j = 0;
let row;
for(; i < arr.length; i++) {
    row = arr[i];
    console.log('row' + i);
    for(j = 0; j < row.length; j++) {
        console.log(row[j]);
    }
}
// row0 a b row1 c d row2 e f

// 稀疏数组：并不含有从0开始的连续索引
let arr1 = [undefined];
let arr2 = new Array(1);
console.log(0 in arr1);  // true
console.log(0 in arr2);  // false
```
    
### 方法

Array.prototype.join(separator)
```javascript
// Array.prototype.join(separator)方法：将数组拼接成字符串，返回字符串，不改变原数组
let arr = ['a', 'b', 'c'];
console.log(arr.join());  // a,b,c
console.log(arr.join('_'));  // a,b,c
function repeatString(str, n) {
    return new Array(n + 1).join(str);
}
console.log(repeatString('a', 3));  // aaa
console.log(repeatString('Hi', 5));  // HiHiHiHiHi
```

Array.prototype.reverse()
```javascript
// Array.prototype.reverse() 将数组逆序，返回数组本身，会改变数组本身
let arr = ['a', 'b', 'c'];
let arr1 = arr;
let arr2 = ['a', 'b', 'c'];
console.log(arr.reverse());  // [ 'c', 'b', 'a' ]
console.log(arr);  // [ 'c', 'b', 'a' ]
console.log(arr1);  // [ 'c', 'b', 'a' ]
console.log(arr2);  // [ 'a', 'b', 'c' ]
```

Array.prototype.sort(sortby）
```javascript
// Array.prototype.sort(sortby)方法：接受一个函数参数(不传参数默认按字符编码顺序)，返回数组本身，会修改原数组
let x = ['f', 'b', 'd', 'e', 'a'];
console.log(x.sort());  // [ 'a', 'b', 'd', 'e', 'f' ]
console.log(x);  // [ 'a', 'b', 'd', 'e', 'f' ]

let y = [13, 24, 51, 3];
console.log(y.sort());  // [ 13, 24, 3, 51 ]

// 传入参数：比较函数(a, b)
// 若 a 小于 b，在排序后的数组中 a 应该出现在 b 之前，则返回一个小于 0 的值。
// 若 a 等于 b，则返回 0。
// 若 a 大于 b，则返回一个大于 0 的值。
function sortNumber(a, b) {
    return a - b;
}
console.log(y.sort(sortNumber));  // [ 3, 13, 24, 51 ]

let z = [{age: 25}, {age: 29}, {age: 40}];
console.log(z.sort(function sortAge(a, b) {
    return a.age - b.age;
}));  // [ { age: 25 }, { age: 29 }, { age: 40 } ]
```
    
Array.prototype.concat(arrayX, arrayY.....)
```javascript
// Array.prototype.concat(arrayX, arrayY.....)方法 连接多个数组，返回一个新的数组，不会改变原数组
let arr = ['a', 'b', 'c'];
console.log(arr.concat('d', 'e'));  // [ 'a', 'b', 'c', 'd', 'e' ]
console.log(arr);  // [ 'a', 'b', 'c' ]
console.log(arr.concat(['d', 'e'], 'f'));  // [ 'a', 'b', 'c', 'd', 'e', 'f' ]
console.log(arr.concat(['d', 'e'], ['f', 'g']));  // 'b', 'c', 'd', 'e', 'f', 'g' ]
console.log(arr.concat([['d', 'e'], 'f']));  // [ 'a', 'b', 'c', [ 'd', 'e' ], 'f' ]
console.log(arr.concat([[['d', 'e'], 'f'], 'g']));  // [ 'a', 'b', 'c', [ [ 'd', 'e' ], 'f' ], 'g' ]
```
    
Array.prototype.slice(start, end)
```javascript
// Array.prototype.slice(start, end)方法：左闭右开截取数组，返回一个新的数组，不改变原数组
let x = ['a', 'b', 'c', 'd', 'e'];
console.log(x.slice(1, 3));  // ['b', 'c']
console.log(x.slice(1));  // ['b', 'c', 'd', 'e']
console.log(x.slice(1, -1));  // ['b', 'c', 'd']
console.log(x.slice(-4, -3));  // ['b']
```

Array.prototype.splice(index, howmany, item1,....itemX)
```javascript
// Array.prototype.splice(index, howmany, item1,....itemX)方法：
// 向/从数组中添加/删除项目，然后返回被删除的项目。会改变原数组
// index参数：必须，整数，添加或删除的位置，左闭合
// howmany参数：可选，设置为0，则不会删除
// item1,.....itemX：可选，向数组中添加的元素

let a = ['a', 'b', 'c', 'd', 'e'];
console.log(a.splice(2));   // [ 'c', 'd', 'e' ]
console.log(a);  // [ 'a', 'b' ]

let b = ['a', 'b', 'c', 'd', 'e'];
console.log(b.splice(2, 1));  // [ 'c' ]
console.log(b);  // [ 'a', 'b', 'd', 'e' ]

let c = ['a', 'b', 'c', 'd', 'e'];
console.log(c.splice(2, 0));  // []
console.log(c);  // [ 'a', 'b', 'c', 'd', 'e' ]

let d = ['a', 'b', 'c', 'd', 'e'];
console.log(d.splice(2, 2, 'f', 'g', ['h', 'i']));  // [ 'c', 'd' ]
console.log(d);  // [ 'a', 'b', 'f', 'g', [ 'h', 'i' ], 'e' ]

// 指定位置插入新的元素
let e = ['a', 'b', 'c', 'd', 'e'];
console.log(e.splice(2, 0, 'f'));  // []
console.log(e); // [ 'a', 'b', 'f', 'c', 'd', 'e' ]

let f = ['a', 'b', 'c', 'd', 'e'];
console.log(f.splice());  // []
console.log(f); // [ 'a', 'b', 'c', 'd', 'e' ]
```
   
Array.prototype.forEach()
```javascript
// Array.prototype.forEach()方法(ES5)：接收函数参数
// 函数的3个参数x: 元素值，index：元素索引，a 数组本身
let x = ['a', 'b', 'c', 'd', 'e'];
x.forEach(function(x, index, a) {
    console.log(x + '_' + index + '_' + a);
});
// a_0_a,b,c,d,e b_1_a,b,c,d,e c_2_a,b,c,d,e d_3_a,b,c,d,e e_4_a,b,c,d,e
```
   
Array.prototype.map()
```javascript
//Array.prototype.map()方法(ES5):接收函数参数,不会修改原数组
let x = ['a', 'b', 'c'];
let y = x.map(function(x) {
    return x + 'like';
});

console.log(x);  // [ 'a', 'b', 'c' ]
console.log(y);  // [ 'alike', 'blike', 'clike' ]
```
 
Array.prototype.filter()
```javascript
// Array.prototype.filter()过滤方法(ES5):接收函数参数，不修改原数组
let x = [1,2,3,4,5,6,7,8,9,10];
let y = x.filter(function(x, index) {
    return index % 3 === 0 || x >= 8;
});
console.log(y);  // [ 1, 4, 7, 8, 9, 10 ]
console.log(x);  // [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
```

Array.prototype.every() & Array.prototype.some()
```javascript
// Array.prototype.every() & Array.prototype.some()判断方法(ES5):接收函数参数，不修改原数组
// every 全部符合条件  some 只需一个符合条件
let arr = [1,2,3,4,5];
console.log(arr.every(function(x) {
    return x < 10;
}));  // true
console.log(arr.every(function(x) {
    return x <= 3;
}));  // false
console.log(arr.some(function(x) {
    return x === 3;
}));  // true
console.log(arr.some(function(x) {
    return x === 100;
}));  // false

```

Array.prototype.reduce(function, index) Array.prototype.reduceRight(function, index)
```javascript
// Array.prototype.reduce(function, index) Array.prototype.reduceRight(function, index)方法
// 接收一个函数参数和一个数组索引参数，并返回一个值，不修改原数组
// 函数参加接收2个参数分别代表2个索引
// 索引参数表示从第index个元素开始
let a = [1, 2, 3];
let sum = a.reduce(function(x, y) {
    return x + y;
}, 0);
console.log(sum);  // 6  1+2返回3 3+3 返回6

let b = [3, 9, 6];
let max1 = b.reduce(function(x, y) {
    console.log(x + '|' + y);
    return x > y ? x : y;
});
// 3|9
// 9|6
console.log(max1);  // 9

let max2 = b.reduceRight(function(x, y) {
    console.log(x + '|' + y);
    return x > y ? x : y;
});
// 6|9
// 9|3
console.log(max2);  // 9
```

Array.prototype.indexOf(value, index) Array.prototype.lastIndexOf(value, index)
```javascript
// Array.prototype.indexOf(value, index) Array.prototype.lastIndexOf(value, index)数字检索方法
// value参数表示要查找的元素值，index表示从第几个索引位置开始查找(闭合)
let arr = ['a', 'b', 'c', 'b', 'a'];
console.log(arr.indexOf('b'));  // 1 在数组中查找是否有元素值为'b'，有则返回这个元素索引
console.log(arr.indexOf('f'));  // -1 如果没有则返回-1
console.log(arr.indexOf('b', 1));  // 1 从第2个元素开始查找，返回索引1
console.log(arr.indexOf('b', 2));  // 3
console.log(arr.indexOf('b', -2));  // 3
console.log(arr.indexOf('b', -4));  // 1
console.log(arr.indexOf('b', -8));  // 1
console.log(arr.lastIndexOf('b'));  // 3
```
    
Array.isArray()
```javascript
// Array.isArray() 判断是否为数组
console.log(Array.isArray([]));  // true
```

## 函数

### 创建

```javascript
// 函数声明
function add1(a, b) {
    a = +a;
    b = +b;
    if (isNaN(a) || isNaN(b)) {
        return;
    }
    return a + b;
}

// 函数表达式
let add2 = function(a, b) {
    // do sth
};

// IEF(立即执行函数表达式)
(function() {
    // do sth
})();

// 函数表达式
return function () {
    // do sth
};

// NFE(命名式函数表达式)
let add3 = function foo(a, b) {
    // do sth
};

```

**函数声明和函数表达式区别：函数声明会前置**
```javascript

let num1 = add1(1, 2);
console.log(num1);
function add1(a, b) {
    a = +a;
    b = +b;
    if (isNaN(a) || isNaN(b)) {
        return;
    }
    return a + b;
}
// 结果为3
```
```javascript
let num = add(1, 2);
console.log(num);
let add = function (a, b) {
    a = +a;
    b = +b;
    if (isNaN(a) || isNaN(b)) {
        return;
    }
    return a + b;
};
// TypeError: add is not a function
```

### call和apply
```javascript
// apply和call方法（浏览器）
// 第一个参数是作为this的对象，如果不是对象，会转换为对象
// call传入的是扁平传入参数
// apply第二个参数树数组
function foo(x, y) {
    console.log(x, y, this);
}

foo.call(100, 1, 2);  // 1, 2, Number(100)
foo.apply(true, [3, 4]);  //3, 4, Boolean(true)
foo.apply(null);  // undefined, undefined, window
foo.apply(undefined);  // undefined, undefined, window
```

### arguments属性
```javascript
function foo(x, y, z) {
    console.log(arguments.length);
}

// arguments.callee()递归
function sum (num){
    if (num <= 1) return 1;
    else return num * arguments.callee(num-1);
}
console.log(sum(4));    //返回结果:24
```

### bind
bind方法：改变this
```javascript
// bind方法
this.x = 9;
var module = {
    x: 81,
    getX: function() {
        return this.x;
    }
};

module.getX();  // 81 this指向module

var getX = module.getX;
getX();  // 9 this指向全局变量

var boundGetX = getX.bind(module);
boundGetX();  // 81  bind改变this指向
```
bind方法：科里化
```javascript
// bind方法
function add(a, b, c) {
    return a+b+c;
}

let func = add.bind(undefined, 100);
func(1, 2);  // 103  undefined传给this， 100传给第一个参数a

let func2 = func.bind(undefined, 200);
func2(10);  // 310 undefined传给this，200传给b
```

bind与new
```javascript
// bind方法与new
function foo() {
    this.b = 100;
    return this.a;
}
let func = foo.bind({a: 1});
func(); // 1  foo.bind后this指向{a: 1} return this.a = 1
new func();  // {b: 100} new调用将返回一个this，this会被初始化一个空对象，并且其原型是foo.prototype,空对象的b属性为100
```

## 闭包

闭包基本概念
```javascript
// 闭包
let n = 999;
function f1() {
    console.log(n);
}
f1();  // 999  函数内部可以访问全局变量

function f2() {
    let n = 999;
}
console.log(n);  // error  函数外部无法访问函数内部局部变量

function f3() {
    n = 999;
}
console.log(n); // 999 因为没有使用var语句，相当于定义一个全局变量

// 简单的闭包原型
function f4() {
    let n = 999;
    function f5() {
        console.log(n);
    }
    return f5
}
let result = f4();
result();  // 999
```

闭包案例
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        #div1 {
            background-color: rosybrown;
        }
        #div2 {
            background-color: aquamarine;
        }
        #div3 {
            background-color: yellowgreen;
        }
    </style>
</head>
<body>

</body>
<script>
    document.body.innerHTML = '<div id="div1">aaa</div><div id="div2">bbb</div><div id="div3">ccc</div>';
    for(var i = 1; i < 4; i++) {
        alert(i);  // 结果为1，2，3
        document.getElementById('div' + i).
                addEventListener('click', function() {
            alert(i);  // 点击div1，div2，div3结果都是4,因为addEventListener的回调函数执行时i都为4
        });
    }
</script>
</html>
```
    
正确写法
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        #div1 {
            background-color: rosybrown;
        }
        #div2 {
            background-color: aquamarine;
        }
        #div3 {
            background-color: yellowgreen;
        }
    </style>
</head>
<body>

</body>
<script>
    document.body.innerHTML = '<div id="div1">aaa</div><div id="div2">bbb</div><div id="div3">ccc</div>';
    for(var i = 1; i < 4; i++) {
        !function(i) {
            alert(i);  // 1,2,3
            document.getElementById('div' + i).
               addEventListener('click', function() {
                   alert(i);  // 1, 2,3 
            });
        }(i);
    }
</script>
</html>
```
    
闭包中的封装
```javascript
(function() {
    let _userID = 23245;
    let _typeID = 'item';
    let exportID = {};

    function converter(userID) {
        return +userID;
    }

    exportID.getUserID = function() {
        return converter(_userID);
    };

    exportID.getTypeID = function() {
        return _typeID;
    };
    global.exportID = exportID;
}());

console.log(exportID.getUserID());  //23245
console.log(exportID._userID);  // undefined

```
    
## this

### 全局下的this
```javascript
// 全局下的this(浏览器下)
console.log(this.window === document);  // true
console.log(this === window);  // true
this.a = 37;
console.log(window.a);  // 37
```

### 一般函数的this
```javascript
// 一般函数的this（浏览器下）
function f1() {
    return this;
}
f1() === window;  // true
function f2() {
    "use strict";  // 严格模式下
    return this
}
f2() === undefined;  // true
```
    
### 作为对象方法的函数的this
```javascript
let o1 = {
    prop: 37,
    f: function() {
        return this.prop;
    }
};
console.log(o1.f());  // 37

let o2 = {prop: 37};
function  independent() {
    return this.prop;
}
o2.f = independent;
console.log(o2.f());  // 37
```

### 原型链上的this
```javascript
let o = {
    f: function() {
        return this.a + this.b;
    }
};
let p = Object.create(o); // p为空对象，其原型会指向o
p.a = 1;
p.b = 4;
console.log(p.f())  // 5
```

### 构造器中的this
```javascript
// 当new的函数构造器没有return返回值或者返回值不是对象时，new返回一个this指向构造器的prototype这样一个空对象
// 如果return返回的是对象 new返回的这是这个对象
function MyClass() {
    this.a = 37;
}
let o = new MyClass();
console.log(o.a);  // 37

function C2() {
    this.a = 37;
    return {a: 38};
}
o = new C2();
console.log(o.a);  // 38
```

### call和apply的this
```javascript
function add(c, d) {
    return this.a + this.b + c + d;
}
let o = {a: 1, b: 2};
add.call(o, 5, 6);  // 1+3+5+7=16
add.apply(o, [10, 20]);  //1+3+10+20=34

function bar() {
    console.log(Object.prototype.toString.call(this));
}
bar.call(7); // [object Number]
```
  
### band和this
```javascript
//bind是es5后，ie9+使用
function f() {
    return this.a;
}
let g = f.bind({a: 'test'});
console.log(g());  // test
let o = {
    a: 37,
    f: f,
    g: g
};
console.log(o.f(), o.g());  // 37, test
```

## 作用域
```javascript
 // 作用域： 全局作用域，函数作用域，eval作用域
let a = 10;
(function() {
    let b = 20;
})();
console.log(a);  // 10
console.log(b);  // error

for(let item in {a: 1, b: 2}) {
    console.log(item);  // a, b
}
console.log(item); // b
```

## ES3中的执行上下文

EC = Execution Context 执行上下文  
变量对象（Variable Object 简写VO）是一个抽象概念，用于存储执行上下文中的变量，函数声明，函数参数
```javascript
var a = 10;
function test(x) {
    var b = 20;
}
test(30);
```

对应的上下文
```textmate
// 整个js环境中的VO
VO(globalContext) = {
    a: 10,
    test: <ref to function>
};
// 执行test(30)的VO
VO(test functionContext) = {
    x: 30,
    b: 20
};
```

全局执行上下文
```textmate
VO(globalContext) === [[global]]
[[global]] = {
    Math : <...>,
    String : <...>,
    isNaN: function() {[Native Code]},
    ...
    ...
    window: global
};

String(10);  //[[global]].String(10)
window.a = 10;  // [[global]].window.a = 10
this.b = 20;  // [[global]].b = 20
```
 
变量初始化阶段
```javascript
// 1, 函数参数（若没有传入，则其值为undefined）
// 2, 函数声明（若函数声明和函数参数冲突，则函数声明会覆盖掉函数参数）
// 3，变量声明（初始化变量值为undefined，若何函数参数，函数声明冲突，会被忽略掉）
function test(a, b) {
    var c = 10;
    function d() {}
    var e = function _e() {};
    (function x() {});
    b = 20;
}
test(10);
```
```textmate
AO(test) = {
    a: 10,
    b: undefined,
    c: undefined,
    d: <ref to func 'd'>,
    e: undefined,
};

```

变量初始化案例1
```javascript
function foo(x, y, z) {
    function x() {}
    console.log(x);
}

foo(100);  // [Function: x]
```
```textmate
AO(foo) = {
    //x: 100,
    y: undefined,
    z: undefined,
    x: <ref to func 'x'>,  // 函数声明x会覆盖掉参数声明 
}
```

变量初始化案例2
```javascript
function foo(x, y, z) {
    function func() {}
    var func;
    console.log(func);
}
foo(100);  // [Function: func]

function foo(x, y, z) {
    function func() {}
    var func = 1;
    console.log(func);
}
foo(100);  // 1  执行阶段会执行func = 1 这个语句
```

代码执行阶段
```javascript
function test(a, b) {
    var c = 10;
    function d() {}
    var e = function _e() {};
    (function x() {})();
    b = 20;
}
```
```textmate
// 初始化阶段
AO(test) = {
    a: 10,
    b: undefined,
    c: undefined,
    d: <ref to func 'd'>,
    e: undefined
};
// 代码执行阶段
AO(test) = {
    a: 10,
    b: 20,
    c: 10,
    d: <reference to FunctionDeclaration 'd'>,
    e: function _e()
};

```

全局执行上下文案例
```javascript
console.log(x);  // [Function: x]
var x = 10;  // 初始化阶段x: 冲突，被忽略
console.log(x);  // 10
x = 20;
function x() {}  // 初始化阶段x: <ref to func 'x'>
console.log(x);  // 20
if (true) {
    var a = 1;  // 初始化阶段a: undefined
} else {
    var b = true;  // 初始化阶段b: undefined
}

console.log(a);  // 1
console.log(b);  // undefined
```

## 继承

### 实现继承的方法
```javascript
function Person() {
}

function Student() {
}

Student.prototype = Person.prototype;  // 错误，会影响被继承类的方法
Student.prototype = new Person();  // 错误，传参问题，因为Student是类，并不是实例

// 正确的方法
Student.prototype = Object.create(Person.prototype);
Student.prototype.constructor = Student;
// Object.create()是es5的方法，下面是fix方法
if(!Object.create) {
    Object.create = function(proto) {
        function F() {}
        F.prototype = proto;
        return new F;
    };
}
```
    
原型的继承方法
```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.sayNo = function() {
        console.log(this.name + ' say no');
    }
}

Person.prototype.hi = function() {
    console.log('Hi, my name is ' + this.name + ',I\'m ' + this.age + ' years old now');
};
Person.prototype.LEGS_NUM = 2;
Person.prototype.ARMS_NUM = 2;
Person.prototype.walk = function() {
    console.log(this.name + ' is walking...');
};

console.log(Person);  // [Function: Person]
console.log(Person.prototype);  // Person { hi: [Function], LEGS_NUM: 2, ARMS_NUM: 2, walk: [Function] }
var Jack = new Person();  // Person构造器没有return对象，所以返回的是this
Jack.sayNo();  // undefined say no
Jack.hi();  // Hi, my name is undefined,I'm undefined years old now
Jack.walk();  // undefined is walking...
console.log(Jack);  // Person { name: undefined, age: undefined, sayNo: [Function] }
console.log(Jack.__proto__);  // Person { hi: [Function], LEGS_NUM: 2, ARMS_NUM: 2, walk: [Function] }

// 以下5行代码是js中对象继承的写法
function Student(name, age, className) {
    Person.call(this, name, age);  // 使用call方法调用父构造器函数
    this.className = className;
}
Student.prototype = Object.create(Person.prototype);   // 这个如果直接使用Student.prototype = Person.prototype，改变Student对象方法会改变Person对象的方法，因为他们指向的是同一个对象
Student.prototype.constructor = Student;

Student.prototype.hi = function() {
    console.log('Hi, my name is ' + this.name + ', I\'m' + this.age + ' years old now, and from ' + this.className + '.');
};
Student.prototype.learn = function(subject) {
    console.log(this.name + ' is learning ' + subject + ' at ' + this.className + '.');
};

var Lussi = new Student('Lussi', 27, 'Class Tree');
Lussi.sayNo();  // Lussi say no
Lussi.hi();  // Hi, my name is Lussi, I'm27 years old now, and from Class Tree.
Lussi.walk();  // Lussi is walking...
Lussi.learn('math');  // Lussi is learning math at Class Tree.
console.log(Lussi);  // Student {name: 'Lussi', age: 27, sayNo: [Function], className: 'Class Tree' }
console.log(Lussi.__proto__);  // Student {constructor: [Function: Student], hi: [Function], learn: [Function] }
console.log(Lussi.__proto__.__proto__);  // Person { hi: [Function], LEGS_NUM: 2, ARMS_NUM: 2, walk: [Function] }
console.log(Lussi.__proto__.__proto__.__proto__);  // {}
console.log(Lussi.__proto__.__proto__.__proto__.__proto__);  // null  
// 改变prototype
Student.prototype.x = 101;  // 会影响已经创建实例的属性
console.log(Jack.x);  // undefined
console.log(Lussi.x);  // 101

Student.prototype = {y: 2};  // 不会影响已经创建实例的属性，但会影响新创建的实例，并且重新修改prototype.y属性也不会改变
console.log(Jack.y); // undefined
console.log(Lussi.y);  // undefined
var Lilei = new Student('Lilei', 24, 'Class three');
console.log(Lilei.x);  // undefined
console.log(Lilei.y);  // 2
Student.prototype.y = 100;
console.log(Lilei.y);  // 100
console.log(Lussi.y);  // undefined
Student.prototype.z = 30;
console.log(Lilei.z);  // 30
console.log(Lussi.z);  // undefined
Person.prototype.z = 40;
console.log(Lilei.z);  // 30
console.log(Lussi.z);  // 40
console.log(Jack.z);  // 40
```
    

### 模拟重载
```javascript
function Person() {
    let args = arguments;
    // 判断第一个传入的是不是对象，因为args[null]也返回object，所有需要判断是否为null
    if(typeof args[0] === 'object' && args[0]) {
        if(args[0].name) {
            this.name = args[0].name;
        }
        if(args[0].age) {
            this.age = args[0].age;
        }
    } else {
        if(args[0]) {
            this.name = args[0];
        }
        if(args[1]) {
            this.age = args[1];
        }
    }
}
Person.prototype.toString = function() {
    console.log('name=' + this.name + ', age=' + this.age);
};

let LiLei = new Person('LiLei', 23);
let Hanmm = new Person({name: 'Hanmm', age: 21});
LiLei.toString();  // name=LiLei, age=23
Hanmm.toString();  // name=Hanmm, age=21

```
    
### 调用子类方法
```javascript
function Person(name) {
    this.name = name;
}
function Student(name, className) {
    this.className = className;
    Person.call(this, name);
}

Person.prototype.init = function() {
    console.log('Person.init');
};
Student.prototype.init = function() {
    console.log('Student.init');
    Person.prototype.init.apply(this, arguments);
};
let LiLei = new Student('LiLei', 'Class four');
console.log(LiLei);  // Student { className: 'Class four', name: 'LiLei' }
LiLei.init();  // Student.init  Person.init

```

### 实现链式调用
```javascript
function ClassManager() {
    console.log('ClassManager');
    this.name = 'ClassName';
}
ClassManager.prototype.addClass = function(str) {
    console.log('class: '  + str + ' added.');
    console.log(this);
    return this;  // this始终指向ClassManger类的实例
};

let manager = new ClassManager();
manager.addClass('A').addClass('B').addClass('C');  //class: A added. ClassManager { name: 'ClassName' } class: B added. ClassManager { name: 'ClassName' } class: C added. ClassManager { name: 'ClassName' }
```
    