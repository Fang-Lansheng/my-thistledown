---
layout:     post
title:      "JavaScript 学习笔记（二）：对象、对象的属性以及对象的方法"
subtitle:   "JavaScript Study Notes (2) : Object, its properties, and its methods"
date:       2019-01-10 10:00:00 +0800
author:     "Thistledown"
header-img: "img/posts/post-bg-js-study-notes.jpg"
catalog: true
tags:
    - JavaScript 学习笔记
---

> 本篇学习笔记是在[慕课网](https://www.imooc.com/)上学习[JavaScript深入浅出](https://www.imooc.com/learn/277)时所做笔记，内容主要来自课程课件及老师讲解，同时穿插自己的一些理解和尝试。部分资料整理自 [MDN Web Docs](https://developer.mozilla.org/)。


## 4.1 对象概述

对象中包含一系列属性，这些属性是**无序**的。每个属性都有一个**字符串** `key` 和对应的 `value`。

```javascript
var obj_0 = { x : 1, y : 2 };
obj_0.x;	// 1
obj_0.y;	// 2

var obj = {};
obj[1] = 1;
obj['1'] = 2;
obj;
// Object {1: 2}

obj[{}] = true;
obj[{x: 1}] = true;
obj;
// Object {1: 2, [object Object]: true}
```

函数 `function`、数组 `Array` 等都是对象。

## 4.2 创建对象、原型链

**对象创建的方法**

1. 字面量

```javascript
var obj1 = { x : 1, y : 2 };
var obj2 = {
    x: 1,
    y: 2,
    o: {	// 嵌套
        z: 3,
        n: 4
    }
};
```

2. `new` / 原型链

```javascript
function foo() {};
foo.prototype.z = 3;

var obj = new foo();
obj.x = 1;
obj.y = 2;
```

原型链：

![](https://ws1.sinaimg.cn/large/006y42ybgy1fz08hx4v69j309p0c0jt5.jpg)

```javascript
obj.x;	// 1
obj.y;	// 2
obj.z;	// 3 (通过原型链向上查找，有属性'z')
typeof obj.toString;	// 'function' (toString 在 Object.prototype 上)
'z' in obj;	// true (z 是 obj 从 foo 中继承过来的)
obj.hasOwnProperty('z');	// false (z 并不在对象中，而是在对象的原型链上)
```

如果赋值：

```javascript
obj.z = 5;
```

原型链：

![](https://ws1.sinaimg.cn/large/006y42ybgy1fz08oezgmej309q0d70uu.jpg)

```javascript
obj.hasOwnProperty('z');	// true
foo.prototype.z;	// (still) 3
obj.z;	// 5

delete obj.z;	// true (在对象 obj 中删除属性 z)
obj.z;	// 3

delete obj.z;	// true (再次删除)
obj.z;	// still 3 (原型链上的属性不会被修改！)
```

3. `Object.create`

`Object.create()` 是一个系统内置的函数，这个函数会接受一个参数（一般是一个对象），它会返回一个新创建的对象，并让这个对象的原型，指向这个参数。

```javascript
var obj = Object.create({ x : 1 });
obj.x;	// 1
typeof obj.toString;	// function
obj.hasOwnProperty('x')	// false
```

![](https://ws1.sinaimg.cn/large/006y42ybgy1fz093tjkqkj30a603edga.jpg)

```javascript
var obj = Object.create(null);
obj.toString;	// undefined
```

## 4.3 属性操作

#### 4.3.1 属性读写

```javascript
var obj = { x : 1, y : 2 };
// 访问对象属性
obj.x;	// 1
obj['y'];	// 2
// 
obj['x'] = 3;
obj.y = 4;
```

属性读写 - 遍历：

```javascript
var obj = { x1: 1, x2 : 2, x3 : 3 };
var i = 1, n = 3;

for (; i <= n; i++) {
    console.log(obj['x' + i]);	// 输出 1, 2, 3
}

var p;
for (p in obj) {	// 用 for … in 方法可能会把原型链上的一些属性遍历出来
    console.log(p);	// 输出 x1, x2, x3（顺序不确定）
    console.log(obj[p]);	// 输出 1, 2, 3（顺序不确定)
}
```

属性读写 - 异常：

```javascript
var obj = { x : 1 };
obj.y;	// undefined
var yz = obj.y.z;	// TypeError: Cannot read property 'z' of undefined
obj.y.z = 2;	// TypeError: Cannot set property 'z' of undefined

// 读取时的判断
var yz;
if (obj.y) {
    yz = obj.y.z;
}
// or
var yz = obj && obj.y && obj.y.z;
```

#### 4.3.2 属性删除

```javascript
var person = { age: 28, title: 'fe' };
delete person.age;	// true
delete person['title'];	// true
person.age;	// undefined
delete person.age;	// true （重复删除）

delete Object.prototype;	// false （不允许删除）
var descriptor = Object.getOwnPropertyDescriptor(Object, 'prototype');	// 获取对象的一个属性中的所有标签
descriptor.configurable;	// false
```

#### 4.3.3 属性检测

```javascript
var cat = new Object;
cat.legs = 4;
cat.name = 'Kitty';

'legs' in cat;	// true
'abc' in cat;	// false
'toString' in cat;	// true (inherited property!)(in 操作符会在原型链中向上查找)

cat.hasOwnProperty('legs');	// true
cat.hasOwnProperty('toString');	// false (该对象上没有该属性，尽管原型链上有)

cat.propertyIsEnumerable('legs');	// true (判断该属性是否可枚举)
cat.propertyIsEnumerable('toString');	// false

// 自定义对象属性
Object.defineProperty(cat, 'price', {enumerable: false, value: 1000});
cat.propertyIsEnumerable('price');	// false
cat.hasOwnProperty('price');	// true

if (cat && cat.legs) {
    // cat 存在，cat.legs 存在
    cat.legs *= 2;
}
if (cat.legs != undefined) {
    // !== undefined, or, !== null 
}
if (cat.legs !== undefined) {
    // only if cat.legs is not undefined
}
```

#### 4.3.4 属性枚举

```javascript
var o = { x: 1, y: 2, z: 3 };
'toString' in o;	// true
o.propertyIsEnumerable('toString');	// false（默认 false）
var key;
for (key in o) {	// 枚举 o 中（enumerable / 可枚举的）属性
    console.log(key);	// x, y, z （不会出现 toString 等属性）
}

var obj = Object.create(o);
obj.a = 4;
var key;
for (key in obj) {
    console.log(key);	// a, x, y, z
}

var obj = Object.create(o);
obj.a = 4;
var key;
for (key in obj) {
    if (obj.hasOwnProperty(key)) {	// 过滤掉原型链上的属性
        console.log(key);	// a
    }
}
```

## 4.4 `get` / `set` 方法

另一种读写属性的方式

```javascript
var man = {
    name: 'Bosn',	// 课程老师的名字 :)
    weibo: '@Bosn',
    get age() {	// get 方法
        return new Date().getFullYear() - 1988;
    },
    set age(val) {	// set方法
        console.log('Age can\'t be set to ' + val);
    }
}
console.log(man.age);	// 31（访问 man.age 时，自动调用 get 方法）
man.age = 100;	// Age cat't be set to 100（为 man.age 赋值时，自动调用 set 方法）
console.log(man.age);	// still 31（调用 get 方法）
```

更复杂的例子：

```javascript
var man = {
    weibo: '@Bosn',
    $age: null,	// $age 与 age 是不同的，这里的 $ 符号表示不在外部暴露这个属性*
    get age() {
        if (this.$age == undefined) {
            return new Date().getFullYear() - 1988;
        } else {
            return this.$age;
        }
    },
    set age(val) {
        val = +val;	// 一元 + 操作符，用以将 val 转化为数字
        // 例如：+123 => 123, +'123' => 123, +'abc' => NaN
        if (!isNaN(val) && val > 0 && val < 150) {
            this.$age = +val;
        } else {
            throw new Error('Incorrect val = ' + val);
        }
    }
}
console.log(man.age);	// 31
man.age = 100;
console.log(man.age);	// 100
man.age = 'abc';	// error: Incorrect val = NaN
```

`get` / `set` 与原型链

```javascript
function foo() {}
Object.defineProperty(foo.prototype, 'z', {
    get: function() { return 1; }
});
var obj = new foo();

obj.z;	// 1
obj.z;	// 10
obj.z;	// still 1
```

![](https://ws1.sinaimg.cn/large/006y42ybgy1fz0cjq3ekwj30bu0djjti.jpg)

这是因为当 `obj` 对象上没有该属性，并且在原型链上查找时发现有对应的 `get` 或 `set` 方法时，尝试对该属性赋值就会调用原型链上的 `get` / `set` 方法，而不会再去给当前对象添加新属性。如果需要给 `obj` 对象添加这样一个属性并实现修改，可以使用以下方法：

```javascript
Object.defineProperty(obj, 'z', {
    value: 100,
    configurable: true	// 默认为 false
});
obj.z;	// 100
delete obj.z;	// true
obj.z;	// back to 1
```

```javascript
var o = {};
Object.defineProperty(o, 'x', { value: 1 });	// writable = false, configurable = false;
var obj = Object.create(o);
obj.x;	// 1
obj.x = 200;
obj.x;	// still 1, can't change it

Object.defineProperty(obj, 'x', {
    writable: true,
    configurable: true,
    value: 100
});
obj.x;	// 100
obj.x = 500;
obj.x;	// 500
```

## 4.5 属性标签

> 属性标签即指属性描述符

#### 4.5.1 获取属性标签

`Object.getOwnPropertyDescriptor(obj, prop)` 

- 该方法返回指定对象上一个自有属性对应的属性描述符

  （自有属性指的是直接赋予该对象的属性，不需要从原型链上进行查找的属性）

- 参数：

  - `obj`：需要查找的目标对象
  - `prop`：目标对象内属性名称 （`String` 类型）

- 返回值：

  - 如果指定的属性存在于对象上，则返回其属性描述符对象（property descriptor），否则返回 `undefined`

- 一个属性描述符是一个记录，由下面属性当中的某些组成：

  - `value`：该属性的值（仅针对数据属性描述有效）
  - `writable`：当且仅当属性的值可以改变时为 `true`（仅针对数据属性描述有效）
  - `get`：获取该属性的访问器函数（getter），如果没有访问器，该值为 `undefined`（仅针对包含访问器或设置器的属性描述有效）
  - `set`：获取该属性的设置器函数（setter），如果没有设置器，该值为 `undefined`（仅针对包含访问器或设置器的属性描述有效）
  - `configurable`：当且仅当指定对象的属性描述可以被改变或者属性可被删除时，为 `true`
  - `enumerable`：当且仅当指定对象的属性可以被枚举出时，为 `true`

示例：

```javascript
Object.getOwnPropertyDescriptor({pro: true}, 'pro');
// Object {value: true, writable: true, enumerable: true, configurable: true}
Object.getOwnPropertyDescriptor({pro: true}, 'pro');
// undefined
```

#### 4.5.2 设置属性标签

`Object.defineProperty(obj, prop, descriptor)` 

- 该方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。
- 参数：
  - `obj`：要在其上定义属性的对象
  - `prop`：要定义或修改的属性的名次（`String` 类型）
  - `descriptor`：将被定义或修改的属性描述符
- 返回值：被传递给函数的对象
- 该方法允许精确添加或修改对象的属性。通过赋值操作添加的普通属性是可枚举的，能够在属性枚举期间呈现出来（`for...in`  或  `Object.keys` 方法）， 这些属性的值可以被改变，也可以被删除。这个方法允许修改默认的额外选项（或配置）。**注意：**默认情况下，使用 `Object.defineProperty()` 添加的属性值是不可修改的。

示例 1：

```javascript
var person = {};
Object.defineProperty(person, 'name', {
    configurable: false,
    writable: false,
    enumerable: true,
    value: 'Bosn Ma'
});

person.name;	// Bosn Ma
person.name = 1;
person.name;	// still Bosn Ma
delete person.name;	//false

Object.defineProperty(person, 'type', {
    configurable: true,
    writable: true,
    enumerable: false,
    value: 'Object'
});

Object.keys(person);	// ["name"] (因为，name 可枚举，type 不可枚举)
```

示例 2：

```javascript
Object.defineProperties(person, {	// 类似于 Object.defineProperty()，可以同时定义多个属性
    title: {value: 'fe', enumerable: true},	// 未声明的属性标签默认都为 false
    corp: {value: 'BABA', enumerable: true},
    salary: {value: 50000, enumerable: true, writable: true}
});

Object.getOwnPropertyDescriptor(person, 'salary');
// Object {value: 50000, writable: true, enumerable: true, configurable: false}
Object.getOwnPropertyDescriptor(person, 'corp');
// Object {value: "BABA", writable: false, enumerable: true, configurable: false}
```

示例 3：

```javascript
Object.defineProperties(person, {	// 更加复杂化
    title: {value: 'fe', enumerable: true},	
    corp: {value: 'BABA', enumerable: true},
    salary: {value: 50000, enumerable: true, writable: true},
    luck: {
        get: function() {
            return Math.random() > 0.5 ? 'good' : 'bad';
        }
    },
    promote: {
        set: function(level) {
            this.salary *= 1 + level * 0.1;
        }
    }
});

Object.getOwnPropertyDescriptor(person, 'salary');
// Object {value: 50000, writable: true, enumerable: true, configurable: false}
Object.getOwnPropertyDescriptor(person, 'corp');
// Object {value: "BABA", writable: false, enumerable: true, configurable: false}
person.salary;	// 50000
person.promote = 2;
person.salary;	// 60000
```

#### 4.5.3 `configurable` 与 `writalbe` 的限制

![](https://ws1.sinaimg.cn/large/006y42ybgy1fz0oqk586hj30rx0db0tv.jpg)

## 4.6 对象标签、对象序列化

#### 4.6.1 对象标签

1. 原型标签 `__proto__`

- `Object.prototype` 的 `__proto__` 属性是一个访问器属性（一个 `getter` 函数和一个 `setter` 函数）， 暴露了通过它访问的对象的内部 `[[Prototype]]` (一个对象或  `null`)，任何一个 `__proto__` 的存取属性都继承于`Object.prototype`。
- 使用 `__proto__` 是有争议的，也不鼓励使用它。因为它从来没有被包括在 EcmaScript 语言规范中，但是现代浏览器都实现了它。

![](https://ws1.sinaimg.cn/large/006y42ybgy1fz0p0r3ra9j30hm0dogna.jpg)

2. `class` 标签

- 表示对象的类型
- 没有直接查看或修改的方式，可以间接通过 `Object.prototype.toString` 的方式去获取

```javascript
var toString = Object.prototype.toString;	// 用以获取每个对象的类型
function getType(o) {return toString.call(o).slice(8, -1);}

toString.call(null);	// "[object Null]"
getType(null);	// "Null"
getType(undefined);	// "Number"
getType(new Number(1));	// "Number"
typeof new Number(1);	// "object"
getType(true);	// "Boolean"
getType(new Boolean(true));	// "Boolean"
typeof new Number(true);	// "object"
```

3. `extensible` 标签

`Object.isExtensible()` 方法判断一个对象是否是可扩展的（是否可以在它上面添加新的属性）

- 参数：
  - `obj`：需要检测的队形
- 返回值：表示给定对象是否可扩展的一个 `Boolean`
- 默认情况下，对象是可扩展的：即可以为他们添加新的属性。以及它们的 `__proto__` 属性可以被更改。`Object.preventExtensions`，`Object.seal` 或 `Object.freeze` 方法都可以标记一个对象为不可扩展（non-extensible）。

`Object.seal()`方法封闭一个对象，阻止添加新属性并将所有现有属性标记为不可配置。当前属性的值只要可写就可以改变。

- 参数：

  - `obj`：将要被密封的对象

- 返回值：被密封的对象

- 尝试删除一个密封对象的属性或者将某个密封对象的属性从数据属性转换成访问器属性，结果会静默失败或抛出`TypeError`（在 `严格模式` 中最常见的，但不唯一）。

  不会影响从原型链上继承的属性。但 `__proto__` 属性的值也会不能修改。

`Object.freeze()` 方法可以冻结一个对象，冻结指的是不能向这个对象添加新的属性，不能修改其已有属性的值，不能删除已有属性，以及不能修改该对象已有属性的可枚举性、可配置性、可写性。

- 参数：

  - `obj`：要被冻结的对象

- 返回值：被冻结的对象（而不是创建一个被冻结的副本）

- 被冻结对象自身的所有属性都不可能以任何方式被修改。任何修改尝试都会失败，无论是静默地还是通过抛出`TypeError`异常（最常见但不仅限于 `严格模式`）。

  数据属性的值不可更改，访问器属性（有getter和setter）也同样（但由于是函数调用，给人的错觉是还是可以修改这个属性）。如果一个属性的值是个对象，则这个对象中的属性是可以修改的，除非它也是个冻结对象。数组作为一种对象，被冻结，其元素不能被修改。没有数组元素可以被添加或移除。

```javascript
var obj = {x: 1, y: 2};
Object.isExtensible(obj);	// true
Object.preventExtensions(obj);
Object.isExtensible(obj);	// false
obj.z = 1;
obj.z;	// undefined (add new property failed)
Object.getOwnPropertyDescriptor(obj, 'x');
// Object {value: 1, writable: true, enumerable: true, configurable: true}
// 原有属性仍然是可以修改、删除的

Object.seal(obj);
Object.getOwnPropertyDescriptor(obj, 'x');
// Object {value: 1, writable: true, enumerable: true, configurable: false}
Object.isSealed(obj);	// true

Object.freeze(obj);
Object.getOwnPropertyDescriptor(obj, 'x');
// Object {value: 1, writable: false, enumerable: true, configurable: false}
Object.isFrozen(obj);	// true

// [CAUTION] Do not affects prototype chain!
```

#### 4.6.2 序列化、其他对象方法

1. 序列化

`JSON.stringify()` 方法是将一个 JavaScript 值（对象或者数组）转换为一个 JSON 字符串，如果指定了 `replacer` 是一个函数，则可以替换值，或者如果指定了 `replacer` 是一个数组，可选的仅包括指定的属性。

```javascript
JSON.stringify(value[, replacer[, space]])
```

- 参数：
  - `value`：将要序列化成一个 JSON 字符串的值
  - `replacer` `可选`：如果该参数是一个函数，则在序列化过程中，被序列化的值的每个属性都会经过该函数的转换和处理；如果该参数是一个数组，则只有包含在这个数组中的属性名才会被序列化到最终的 JSON 字符串中；如果该参数为null或者未提供，则对象所有的属性都会被序列化；关于该参数更详细的解释和示例，请参考[使用原生的 JSON 对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_native_JSON#The_replacer_parameter)一文。
  - `space` `可选`：指定缩进用的空白字符串，用于美化输出（pretty-print）；如果参数是一个数字，它代表有多少的空格；上限为 10。该值若小于 1，则意味着没有空格；如果该参数为字符串（字符串的前十个字母），该字符串将被作为空格；如果该参数没有提供（或者为 `null`）将没有空格。
- 返回值：一个表示给定值的 **JSON 字符串**
- 关于序列化，下面有五点注意事项：
  - 非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中
  - 布尔值、数字、字符串的包装对象在序列化过程中会自动转换为对应的原始值
  - `undefined`、任意的函数以及 symbol 值，在序列化中会被忽略（出现在非数组对象的属性值中）或者被转换成 `null` （出现在数组中时）
  - 所有以 symbol 值为属性键的属性都会被完全忽略掉，即使 `replacer` 参数中强制指定包含了它们
  - 不可枚举的属性会被忽略

`JSON.parse()` 方法用来解析JSON字符串，构造由字符串描述的JavaScript值或对象。提供可选的 reviver 函数用以在返回之前对所得到的对象执行变换（操作）。

```javascript
JSON.parse(text[, reviver])
```

- 参数：
  - `text`：将要被解析成 JavaScript 值的字符串，关于 JSON 的语法格式，请参考：[`JSON`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON)。
  - `reviver` `可选`：转换器，如果传入该参数（函数），可以用来修改解析生成的原始值，调用时机在 parser 函数返回之前
- 返回值：`Object` 类型，对应给定的 JSON 文本的对象 / 值
- 异常：若传入的字符串不符合 JSON 规范，则会抛出 `SyntaxError` 异常

```javascript
var obj = {x: 1, y: true, z: [1, 2, 3], nullVal: null};
JSON.stringify(obj);	// "{"x": 1, "y": true, "z": [1, 2, 3], "nullVal": null}"

obj = {val: undefined, a: NaN, b: Infinity, c: new Date()};
JSON.stringify(obj);	// "{"a": null, "b": null, "c": "2019-01-09T14:15:43.910Z"}"

obj = JSON.parse('{"x": 1}');
obj.x;	// 1
```

2. 序列化 - 自定义

```javascript
var obj = {
    x: 1,
    y: 2,
    o: {
        o1: 1,
        o2: 2,
        toJSON: function() {
            return this.o1 + this.o2;
        }
    }
};
JSON.stringify(obj);	// "{"x": 1, "y": 2, "o": 3}"
```

3. 其他对象方法

```javascript
var obj = {x: 1, y: 2};
obj.toString();	// "[object Object]"
obj.toString = function() {return this.x + this.y;};	// 自定义 toString

'Result ' + obj;	// "Result 3", by toString
+obj;	// 3, from toString

obj.valueOf = function() {return this.x + this.y + 100;};	// 自定义 valueOf
+obj;	// 103, from valueOf
'Result ' + obj;	// still "Result 103"
```

无论一元操作符 `+` 还是作为字符串拼接的二元操作符 `+`，在具体操作时都会尝试把对象转换为基本类型。它会先去找 `valueOf` ，如果其返回基本类型，则会以该值作为结果繁殖，当 `valueOf` 不存在或返回对象时，就会去找 `toString`。若二者都没存在或都返回对象，则会报错。