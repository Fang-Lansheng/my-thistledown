---
layout:     post
title:      "JavaScript 学习笔记（一）：数据类型、表达式、运算符和语句"
subtitle:   "JavaScript Study Notes (1) : Date Types, Expression, Operator & Statement"
date:       2019-01-09
author:     "Thistledown"
header-img: "img/posts/post-bg-js-study-notes.jpg"
catalog: true
tags:
    - JavaScript学习笔记
---

> 本篇学习笔记是在[慕课网](https://www.imooc.com/)上学习[JavaScript深入浅出](https://www.imooc.com/learn/277)时所做笔记，内容主要来自课程课件及老师讲解，同时穿插自己的一些理解和尝试。通过其他渠道搜集到的资料均已注明。

## 一、数据类型

~~有意思的~~特性：

**![](https://ws1.sinaimg.cn/large/006y42ybly1fwrwl9q9fjj30je0feq57.jpg)**

#### 1.1 六种数据类型

- 五种原始类型：
  - `number`
  - `string`
  - `Boolean`
  - `null`
  - `undefined`
- 一种对象类型
  - `object`：
    - `function`
    - `Array`
    - `Date`
    - …

#### 1.2 隐式转换

- 字符串拼接：

```javascript
var x = "The answer is " + 42;	// "The answer is 42"
var y = 42 + ' is the answer';	// "42 is the answer"
```

- 巧用 `+` / `-` 规则转换类型：
  - `num - 0`：num 转换为数字类型
  - `num + ''`：num 转换为字符串类型
- `a == b`
  - 类型相同，同 `===`
  - 类型不同时，尝试类型转换和比较
    - `null == undifined` 相等
    - `number == string` 转 number
    - `boolean = ?` 转 number
    - `object == number | string` 尝试将对象转为基本类型
- `a === b`（严格等于）
  - 首先判断 `=` 两边的类型。类型不同时，返回 `false`
    - `null === null`
    - `undefined === undefined` 
    - `NaN ≠ NaN`
    - `new Object() ≠ new Object()`

#### 1.3 包装对象

```javascript
var str = 'string'	// str 为字符串对象
var strObj = new String('string')	// strObj 为对象类型（String类型对应的一个包装类）
```

#### 1.4 类型检测

- `typeof`

  - 返回一个字符串
  - 适合函数对象和基本类型的判断，遇到 `null` 失效（返回 `Object`）。

- `instanceof`

  - `instanceof` 是 JavaScript 的一个二元操作符，类似于 `==`，`>`，`<` 等操作符

  - `instanceof` 是 JavaScript 的保留关键字

  - 它的作用是测试它左边的对象是否是它右边的类的实例，返回 `boolean` 的数据类型

  - 适合于自定义对象，也可以用来检测原生对象

  - 注意：不同 `window` 或 `iframe` 之间的对象类型检测**不能**使用 `instanceof`！

    ```javascript
    [1, 2] instanceof Array === true
    new Object() instanceof Array === false
    ```

- Object.prototype.toString

  - 通过 `{}.toString` 拿到，适合内置对象和基元类型，遇到 null 和 undefined 失效（IE6、7、8等返回 [object Object]）

  ```javascript
  Object.prototype.toString.apply([]) === '[object Array]';
  Object.prototype.toString.apply(function(){}) === '[object Function]';
  Object.prototype.toString.apply(null) === '[object Null]';
  Object.prototype.toString.apply(Undefined) === '[object Undefined]';
  ```

- constructor

- duck type



## 二、表达式和运算符

#### 2.1 表达式

> *《 JavaScript 权威指南（第 6 版）》：*
>
> **表达式** (expression) 是 JavaScript 中的一个短语，JavaScript 解释器会将其计算（evaluate）出一个结果。



1. 原始表达式：

- 常量、直接量：`3.14`、`“test”`
- 关键字：`null`、`this`、`true`
- 变量：`i`、`j`、`k`

2. 复合表达式：

![](https://ws1.sinaimg.cn/large/006y42ybgy1fz08ikyzivj30ln0afwfp.jpg)

3. 初始化表达式：

```javascript
// [1, 2]
new Array(1, 2);
// [1, , , 4]
[1, undefined, undefined, 4];
// { x : 1, y : 2 }
var o = new Object;
o.x = 1;
o.y = 2;
```

4. 函数表达式：

```javascript
var f = function() {};
(function() {
    console.log('hello world');
});
```

5. 属性访问表达式：

```javascript
var o = { x : 1 };
0.x;
0['x'];
```

6. 调用表达式：`func();`
7. 对象创建表达式：

```javascript
new Func(1, 2);
new Object;
```

#### 2.2 运算符

1. 一元运算符：`+num`
2. 二元运算符：`a + b`
3. 三元运算符：`c ? a : b`

按功能分类：

- 赋值：`x += 1`
- 比较：`a == b`
- 算数：`a - b`
- 位：`A | B`
- 逻辑：`exp1 && exp2`
- 字符串：`“a” +"b"`
- 特殊：`delete obj.x`

其中，特殊运算符有：

- 条件运算符 `c ? a : b`

  ```javascript
  var val = true ? 1 : 2;
  // val = 1
  var val = false ? 1 : 2;
  // val = 2
  ```

- 逗号运算符 `a, b`：会从左到右依次计算表达式的值，最后会取最右边的值

  ```javascript
  var val = (1, 2, 3);
  // val = 3
  ```

- `delete`：删除一个 `configurable` 为 `true` 的私有属性

  ```javascript
  var obj = { x : 1 };
  obj.x;	// 1
  delete obj.x;
  obj.x;	// undefined
  ```

  ```javascript
  var obj = {};
  Object.defineProperty(obj, 'x', {
      configurable: false,
      value: 1
  });
  delete obj.x;	// false
  obj.x;	// 1
  ```

- `in`：对象能够访问到属性时返回 `true`

  ```javascript
  window.x = 1;
  'x' in window;	// true
  ```

- `instanceof`, `typeof`

  ```javascript
  {} instanceof Object; 	// true
  typeof 100 === 'number';// true
  ```

- `new`

  ```javascript
  function Foo() {};
  Foo.prototype.x = 1;
  var obj = new Foo();
  obj.x;	// 1
  obj.hasOwnProperty('x');	// false
  obj.__proto__.hasOwnProperty('x');	// true
  // x 是属于对象原型上的属性，而非其本身的属性
  // 涉及到原型链的知识，之后会继续学习
  ```

- `this`

  ```javascript
  this;	// window（浏览器）
  var obj = {
      func: function(){ return this; }
  };
  obj.func();	// obj
  ```

- `void`

  ```javascript
  void 0;	// undefined
  void(0);// undefined
  ```

运算符优先级：

| 优先级 | 运算类型               | 关联性   | 运算符           |
| :----- | ---------------------- | -------- | ---------------- |
| 20     | 圆括号                 | n/a      | `(…)`            |
| 19     | 成员访问               | 从左到右 | `… . …`          |
|        | 需要计算的成员访问     | 从左到右 | `…[…]`           |
|        | `new`（带参数列表）    | n/a      | `new …(…)`       |
|        | 函数调用               | 从左到右 | `…(…)`           |
| 18     | `new`（无参数列表）    | 从右到左 | `new …`          |
| 17     | 后置递增（运算符在后） | n/a      | `… ++`           |
|        | 后置递减（运算符在后） |          | `… --`           |
| 16     | 逻辑非                 | 从右到左 | `! …`            |
|        | 按位非                 |          | `~ …`            |
|        | 一元加法               |          | `+ …`            |
|        | 一元减法               |          | `- …`            |
|        | 前置递增               |          | `++ …`           |
|        | 前置递减               |          | `-- …`           |
|        | `typeof`               |          | `typeof …`       |
|        | `void`                 |          | `void …`         |
|        | `delete`               |          | `delete …`       |
|        | `await`                |          | `await …`        |
| 15     | 幂                     | 从右到左 | `… ** …`         |
| 14     | 乘法                   | 从左到右 | `… * …`          |
|        | 除法                   |          | `… / …`          |
|        | 取模                   |          | `… % …`          |
| 13     | 加法                   | 从左到右 | `… + …`          |
|        | 减法                   |          | `… - …`          |
| 12     | 按位左移               | 从左到右 | `… << …`         |
|        | 按位右移               |          | `… >> …`         |
|        | 无符号右移             |          | `… >>> …`        |
| 11     | 小于                   | 从左到右 | `… < …`          |
|        | 小于等于               |          | `… <= …`         |
|        | 大于                   |          | `… > …`          |
|        | 大于等于               |          | `… >= …`         |
|        | `in`                   |          | `… in …`         |
|        | `instanceof`           |          | `… instanceof …` |
| 10     | 等号                   | 从左到右 | `… == …`         |
|        | 非等号                 |          | `… != …`         |
|        | 全等号                 |          | `… === …`        |
|        | 非全等号               |          | `… !== …`        |
| 9      | 按位与                 | 从左到右 | `… & …`          |
| 8      | 按位异或               | 从左到右 | `… ^ …`          |
| 7      | 按位或                 | 从左到右 | `… | …`          |
| 6      | 逻辑与                 | 从左到右 | `… && …`         |
| 5      | 逻辑或                 | 从左到右 | `… || …`         |
| 4      | 条件运算符             | 从右到左 | `… ? … : …`      |
| 3      | 赋值                   | 从右到左 | `… = …`          |
|        |                        |          | `… += …`         |
|        |                        |          | `… -= …`         |
|        |                        |          | `… *= …`         |
|        |                        |          | `… /= …`         |
|        |                        |          | `… %= …`         |
|        |                        |          | `… <<= …`        |
|        |                        |          | `… >>= …`        |
|        |                        |          | `… >>>= …`       |
|        |                        |          | `… &= …`         |
|        |                        |          | `… ^= …`         |
|        |                        |          | `… |= …`         |
| 2      | `yield`                | 从右到左 | `yield …`        |
|        | `yield*`               |          | `yield* …`       |
| 1      | 展开运算符             | n/a      | `... …`          |
| 0      | 逗号                   | 从左到右 | `… , …`          |

> 优先级从高到低排列，参考 [developer.mozilla.org](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)



## 三、语句、严格模式

> JavaScript 程序由语句组成，语句遵守特定的语法规则。
>
> 例如：`if` 语句，`while` 语句，`with` 语句等等。

下面介绍一些主要语句：

#### 3.1 `block` 语句

块语句常用语组合 0 ~ n 个语句，用一对 `{}` 定义

```javascript
{
    // 语句 1;
    // 语句 2;
    // ……
    // 语句 n;
}
```

请注意：**没有块级作用域**

#### 3.2 `var` 语句

```javascript
function foo() {
    var a = b = 1;	// b 相当于隐式创建了的一个全局变量
}
foo();

console.log(typeof a);	// 'undefined'
console.log(typeof b);	// 'number'

// 正确写法
var a = 1, b = 1;
```

#### 3.3 `try - catch` 语句

提供了一个异常捕获的机制

```javascript
try {	                // 首先执行 try 块中的代码
    throw "test";
}	
catch (ex) {	        // 如果抛出异常，则执行 catch 块中的语句
    console.log(ex);	// "test"
}   
finally {               // 无论如何最后都会执行 finally
    console.log('finally');
}

// 执行结果
// test
// finally
```

`try - catch` 的一个嵌套例子：

```javascript
try {
    try {
        throw new Error('oops');
    }
    finally {
        console.log('finally');
    }
}
catch (ex) {
    console.error('outer', ex.message);
}

// 执行结果为：
// finally
// outer oops
```

又一个例子：

```javascript
try {
    try {
        throw new Error('oops');
    }
    catch (ex) {
        console.error('inner', ex.message);
    }
    finally {
        console.log('finally');
    }
}
catch (ex) {
    console.error('outer', ex.message);
}

// 执行结果为
// inner oops
// finally
```

再来一个更复杂的例子：

```javascript
try {
    try {
        throw new Error('oops');
    }
    catch (ex) {
        console.error('inner', ex.message);
        throw ex;
    }
    finally {
        console.log('finally');
    }
}
catch (ex) {
    console.error('outer', ex.message);
}

// 执行结果为
// inner oops
// finally
// outer oops
```

#### 3.4 `function` 语句

`function` 语句用来定义函数对象，如：

```javascript
function fd() {
    // do sth.
    return true;
}
```

用 `function` 语句定义的函数对象称之为 `函数声明`，而与之对应的另一种叫做 `函数表达式`，如：

```javascript
var fe = function() {
    // do sth.
};
```

函数声明与函数表达式的区别有很多，其中主要的一点在于在于：函数声明会被预先处理，或者说 `函数前置`：

```javascript
fd();	// true
function fd() {
    // do sth.
    return true;
}

fe();	// TypeError
var fe = function() {
    // do sth.
}
```

除了这两种方式之外，还可以通过 `new function(){}` 构造器的方式去创建函数对象，它们之间的区别会在之后展开讨论。

#### 3.5 `for … in` 语句

返回所有能够访问到的属性

```javascript
var p;
var obj = { x : 1, y : 2 };

for (p in obj) {	// 对 obj 对象中的属性进行遍历
    // 1. 顺序不确定
    // 2. enumerable 为 false 时不会出现
    // 3. for in 对象属性时受原型链影响
}
```

#### 3.6 `switch` 语句

```javascript
switch(val) {
    case 1:
        console.log(1);
        break;
    case 2:
        console.log(2);
        break;
    default:
        console.log(0);
        break;
}

switch (val) {
    case 1:
    case 2:
    case 3:
        console.log(123);
        break;
    case 4:
    case 5:
        console.log(45);
        break;
    default:
        console.log(0);
        break;
}
```

#### 3.7 循环语句

```javascript
// while 循环
while (isTrue) {
    // do sth.
}

// do … while 循环
do {
    // do sth.
} while (isTrue)
    
// for 循环
var i;
for (i = 0; i < n; i ++) {
    // do sth.
}
```

#### 3.8 `with` 语句

可以修改当前作用的作用域

```javascript
with ({ x : 1 }) {
    console.log(x);
}

with (document.forms[0]) {
    console.log(name.value);	// 相当于 console.log(document.forms[0].name.value);
}
```

当然，这里完全可以通过定义一个变量来代替：

```javascript
var form = document.forms[0];
console.log(form.name.value);
```

JavaScript 中已经不建议使用 `with` 了：

- 让 JS 引擎优化更难
- 可读性差
- 可被变量定义代替
- 严格模式下被禁用

#### 3.9 JavaScript 严格模式

严格模式是一种特殊的执行模式，它部分修复了语言上的**不足**，提供更强的**错误检查**，并增强**安全性**。

进入严格模式：

```javascript
function func() {
    'use strict';	// 进入严格模式的指令
}

// or
'use strict';
function func() {
    // do sth.
}
```

与普通模式的一些区别：

- 不允许使用 `with`：

  ```javascript
  !function() {
      'use strict';
      with ({ x : 1 }) {
          console.log(x);
      }
  }();
  // SyntaxError（语法错误）
  ```

- 不允许未声明的变量被赋值：

  ```javascript
  !function() {
      'use strict';
      x = 1;
      console.log(window.x);
  }();
  // ReferenceError
  ```

- `arguments` 变为参数的静态副本：

  ```javascript
  !function(a) {
      arguments[0] = 100;
      console.log(a);	
  }(1);	// 1 => 100; 不传 => undefined
  // 100
  
  !function(a) {
      'use strict';
      arguments[0] = 100;	// arguments 变为参数的静态副本
      console.log(a);	
  }(1);
  // 1
  
  !function(a) {
      'use strict';
      arguments[0].x = 100;	// 如果参数是对象，那么修改对象的属性依然会影响
      console.log(a.x);	
  }({ x : 1 })
  // 100
  ```

- `delete` 参数、函数名报错：

  ```javascript
  !function(a) {
      console.log(delete a);	
  }(1);
  // false（删除失败）
  
  !function(a) {
      'use'
      delete a;
  }
  // SyntaxError
  ```

  `delete` 不可配置的属性报错：

  ```javascript
  !function(a) {
      var obj = {};
      Object.defineProperty(obj, 'a', {configurable: false});
      console.log(delete obj.a);	
  }(1);
  // false（删除失败）
  
  !function(a) {
      'use strict';
      var obj = {};
      Object.defineProperty(obj, 'a', {configurable: false});
      console.log(delete obj.a);	
  }(1);
  // TypeError
  ```

- 对象字面量重复属性名报错：

  ```javascript
  !function() {
      var obj = { x : 1, x : 2 };
      console.log(obj.x);
  }();
  // 2
  
  !function() {
      'use strict';
      var obj = { x : 1, x : 2 };
  }();
  // SyntaxError
  ```

- 禁止八进制字面量

  ```javascript
  !function() {
      console.log(0123);	// 八进制值
  }
  // 83
  
  !function() {
      'use strict';
      console.log(0123);
  }
  // SyntaxError
  ```

- `eval`、`arguments` 变为关键字，不能作为变量、函数名：

  ```javascript
  !function() {
      function eval() {}
      console.log(eval);
  }();
  // function eval() {}
  
  !function() {
      'use strict';
      function eval() {}
  }();
  // SyntaxError
  ```

  `eval` 独立作用域：

  ```javascript
  !function() {
      eval('var evalVal = 2;');
      console.log(typeof evalVal);
  }();
  // number
  
  !function() {
      'use strict';
      eval('var evalVal = 2;');
      console.log(typeof evalVal);
  }();
  // undefined
  ```

- 一般函数调用时（不是对象的方法调用，也不使用 `apply` / `call` / `bind` 等修改 `this`）`this` 指向 `null`，而不是全局对象

- 若是有 `apply` / `call`，当传入 `null` 或者 `undefined` 时，`this` 将指向 `null` 或 `undefined`，而不是全局对象

- 试图修改不可写属性`writable = false`、在不可扩展的对象上添加属性时报 `TypeError`，而不是忽略

- `arguments.caller`、`arguments.callee` 被禁用

- ……

严格模式是向上兼容的，对于编写高质量、健壮的代码有很大的帮助。