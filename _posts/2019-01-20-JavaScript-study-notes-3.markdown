---
layout:     post
title:      "JavaScript 学习笔记（三）：数组、数组操作以及数组方法"
subtitle:   "JavaScript Study Notes (3) : Array, its operations, and its methods"
date:       2019-01-20
author:     "Thistledown"
header-img: "img/posts/post-bg-js-study-notes.jpg"
catalog: true
tags:
    - JavaScript 学习笔记
---

> 本篇学习笔记是在[慕课网](https://www.imooc.com/)上学习[JavaScript深入浅出](https://www.imooc.com/learn/277)时所做笔记，内容主要来自课程课件及老师讲解，同时穿插自己的一些理解和尝试。部分资料整理自 [MDN Web Docs](https://developer.mozilla.org/)。

数组是值的**有序**集合。每个值叫做元素，每个元素在数组中都有数字位置编号，也就是索引。JavaScript 中的数组是**弱类型**的，数组中可以含有不同类型的元素。数组元素甚至可以是对象或其他数组。

```javascript
// 例如
var arr = [1, true, null, undefined, {x: 1}, [1, 2, 3]];
arr[0];	// 1
arr[3];	// undefined
arr[4].x;	// 1
arr[5][1];	// 2
```

## 5.1 创建数组 & 数组操作

#### 创建数组 - 字面量

```javascript
var BAT = ['Baidu', 'Alibaba', 'Tencent'];
var students = [{name: 'Bosn', age: 27}, {name: 'Nunnly', age: 3}];
var arr = ['Nunnly', 'is', 'big', 'keng', 'B', 123, true, null];

var commasArr1 = [1, , 2];	// 1, undefined, 2
var commasArr2 = [,,];	// undefined * 2，不赞同这种定义方式
```

数组的大小是有限制的：size from 0 to 4,294,967,295 ($2^{23} - 1$)

#### 创建数组 - new Array

```javascript
var arr = new Array();
var arrWithLength = new Array(100);	// undefined * 100
var arrLikesLiteral = new Array(true, false, null, 1, 2, 'hi');	// 等价于 [true, false, null, 1, 2, 'hi']
```

注意：`Array` 的构造器如果不使用 `new` 也是等价的

#### 数组操作 - 数组元素读写

```javascript
var arr = [1, 2, 3, 4, 5];
arr[1];	// 2
arr.length;	// 5

arr[5] = 6;	// 动态添加数组元素
arr.length;	// 6
delete arr[0];
arr[0];	// undefined
arr.length;	// 6
```

#### 数组操作 - 数组元素增删

JavaScript 中的数组是动态的，不需要指定大小。数组对象的 `lenght` 属性也会根据数组的情况更新。

```javascript
var arr = [];
arr[0] = 1;
arr[1] = 2;
arr.push(3);
arr;	// [1, 2, 3]

arr[arr.length] = 4;	// equals to arr.push(4)
arr;	// [1, 2, 3, 4]

arr.unshift(0);	// 将一个或多个元素添加到数组的开头，并返回该数组的新长度
arr;	// [0, 1, 2, 3, 4]

delete arr[2];
arr;	// [0, 1, undefined, 3, 4]
arr.length;	// 5
2 in arr;	// false

arr.length -= 1;
arr;	// [0, 1, undefined, 3], 4 is removed

arr.pop();	// 3 returned by pop
arr;	// [0, 1, undefined], 3 is removed

arr.shift;	// 0 returned by shift
arr;	// [1, undefined]
```

#### 数组操作 - 数组迭代

```javascript
var i = 0, n = 10;
var arr = [1, 2, 3, 4, 5];
for (; i < n; i++) {
    console.log(arr[i]);	// 1, 2, 3, 4, 5
}

for (i in arr) {
    console.log(arr[i]);	// 1, 2, 3, 4, 5
}

Array.prototype.x = 'inherited';	// 在数组对象的原型中增加一个属性 x
for (i in arr) {
    console.log(arr[i]);	// 1, 2, 3, 4, 5, inherited（顺序不确定）
}

for (i in arr) {
    if (arr.hasOwnProperty(i)) {	// 过滤掉原型链上的属性
        console.log(arr[i]);	// 1, 2, 3, 4, 5（顺序不确定）
    }
}
```

## 5.2 二维数组 & 稀疏数组

#### 二维数组

JavaScript 中的数组的元素也可以是数组，可以定义一个数组，它的每一个元素又是一个数组，这样就形成了二维数组。

```js
var arr = [[0, 1], [2, 3], [4, 5]];
var i = 0, j = 0;
var row;
for (; i < arr.length; i++) {
    row = arr[i];
    console.log('row ' + i);
    for (j = 0; j < row.length; j++) {
        console.log(row[j]);
    }
}

// RESULT:
// row 0
// 0
// 1
// row 1
// 2
// 3
// row 2
// 4
// 5
```

#### 稀疏数组

稀疏数组（sparse array）并不含有从 0 开始的连续索引。一般 length 属性值比实际元素个数大。在 `Java`，`C++` 等中的数组是一段连续的存储空间，元素与元素之间没有空隙，但是在 `JavaScript` 中允许存在有空隙的数组。

```javascript
var arr1 = [undefined];
var arr2 = new Array(1);
0 in arr1;	// true
0 in arr2;	// false
```

这是因为，如控制台所示（下图），数组 `arr1` 是有一个元素（值为 `undefined`）的；而数组 `arr2` 却是一个由 `new Array` 方法构造的一个长度为 1 的数组，第 0 元素的 key 是不存在的。

![kCnii4.png](https://s2.ax1x.com/2019/01/19/kCnii4.png)

我们继续对数组进行操作，控制台会出现以下结果：

```javascript
arr1.length = 100;
arr1[99] = 123;
99 in arr1;	// true
98 in arr2;	// false
```

![kCnKoD.png](https://s2.ax1x.com/2019/01/19/kCnKoD.png)

类似地，也可以这样的方式创建稀疏数组：

```javascript
var arr = [, ,];
0 in arr;	// false
```

稀疏数组在实际应用中并不常见。在遍历稀疏数组时，需要用 `in` 操作符或者“是否为 `undefined`”的方式作相应的判断。

通过查阅[相关资料](https://www.teakki.com/p/57dfbcbad3a7507f975efe60)，笔者了解到，JavaScript 实际上并没有常规的数组，所有的数组其实就是个对象，只不过会自动管理一些 “数字” 属性和 length 属性罢了。说的更直接一点，JavaScript 中的数组根本没有索引，因为索引应该是数字，而 JavaScript 中数组的索引其实是字符串。`arr[1]` 其实就是 `arr["1"]`，若令 `arr["1000"] = 1`，`arr.length` 也会自动变为 1001。这些表现的根本原因就是，JavaScript 中的对象就是字符串到任意值的键值对。注意键只能是字符串。



## 5.3 数组方法

一般的对象都有一个原型，或者其原型链最终都指向 `Object.prototype`，因此我们才能使用 `hasOwnProperty` 等来自 `Object.prototype` 的方法。数组也是一样的，它的原型来自 `Array.prototype`。从 MDN 上，我们可以看到 `Array.prototype` 提供了大量的数组操作方法。

![kCMlYF.png](https://s2.ax1x.com/2019/01/19/kCMlYF.png)



接下来就针对其中一些方法进行了解。

#### `Array.isArray()`

`Array.isArray()` 用于确定传递的值是否是一个 `Array`。

```javascript
Array.isArray(obj);
```

- 参数：
  - `obj`：需要检测的值
- 返回值：如果对象是 `Array`，则为 `true`；否则返回 `false`

```javascript
Array.isArray([]);	// true

// 其他判断方式
[].instanceof Array;	// true
({}).toString.apply([]) === '[object Array]';	// true
[].constructor === Array;	// true
```

#### `Array.prototype.concat()`

`concat()` 方法用于合并两个或多个数组。该方法不会更改现有数组，而是返回一个新数组。

```javascript
var new_array = old_array.concat([value1[, value2[, ...[, valueN]]]])
```

- 参数：
  - `valueN` ：用于连接到新数组的数组 / 值
- 返回值：一个新的 `Array` 实例
- 描述：
  - `concat` 方法创建一个新的数组，它由被调用的对象中的元素组成，每个参数的顺序依次是该参数的元素（如果参数是数组）或者参数本身（如果参数不是数组）。它不会递归到嵌套数组参数中
  - `concat` 方法不会改变 `this` 或任何作为参数提供的数组，而是返回一个浅拷贝（shallow copy），它包含与元是数组相结合的相同元素的副本。原始数组的元素将复制到新数组中，如下所示：
    - 对象引用（而不是实际对象）：`contac` 将对象引用复制到新数组中。原始数组和新数组都引用相同的对象。也就是说，如果引用的对象被修改，则更改对于新数组和原始数组都是可见的。其中包括数组参数中那些也是数组的元素。
    - 数据类型如字符串、数组和布尔（不是 `String`、`Number` 或 `Boolean` 对象）：`concat` 将字符串和数字的值复制到新数组中
- 数组 / 值在连接时保持不变。此外，对于新数组的任何操作（当且仅当元素不是对象引用时）都不会对原始数组产生影响，反之亦然

```javascript
var arr = [1, 2, 3];
arr.concat(4, 5);	// [1, 2, 3, 4, 5]
arr;	// [1, 2, 3]，原数组未被修改

arr.concat([10, 11], 13);	// [1, 2, 3, 10, 11, 13]，“参数是数组”
arr.concat([1, [2, 3]]);	// [1, 2, 3, 1, [2, 3]]，“参数值数组” & “不会递归到嵌套数组参数中”
```

#### `Array.prototype.every()`
`every()` 检查数组的每个元素是否都通过了指定函数的测试。

```javascript
arr.every(function callback(currentValue, index, array), thisArg);
```

- 参数：
  - `callback`：用来测试数组每个元素的函数。它接受三个参数：
    - `currentValue`：数组中当前正在处理的元素的值
    - `index` `可选` ：数组中当前正在处理的元素的索引
    - `array` `可选` ：调用了 `every` 的数组
  - `thisArg` `可选` ：执行 `callback` 函数时使用的 `this` 值
- 描述：
  - `every` 方法为数组中的每个元素执行一次 `callback` 函数，直到它找到一个使 `callback` 返回 `false`（表示可转换为布尔值 `false` 的值）的元素。如果发现了一个这样的元素，`every` 方法将会立即返回 `false`。否则，`callback` 为每一个元素返回 `true`，`every` 就会返回 `true`。`callback` 只会为那些已经被赋值的索引调用。不会为那些被删除或从来没被赋值的索引调用
  - `callback` 被调用时传入三个参数：元素值，元素的索引，原数组
  - 如果为 `every` 提供一个 `thisArg` 参数，则该参数为调用 `callback` 时的 `this` 值。如果省略该参数，则 `callback` 被调用时的 `this` 值，在非严格模式下为全局对象，在严格模式下传入 `undefined`
  - `every` 遍历的元素范围在第一次调用 `callback` 之前就已确定了。在调用 `every` 之后添加到数组中的元素不会被 `callback` 访问到。如果数组中存在的元素被更改，则他们传入 `callback`的值是 `every` 访问到他们那一刻的值。那些被删除的元素或从来未被赋值的元素将不会被访问到
  - `every` 和数学中的"所有"类似，当所有的元素都符合条件才返回 `true`。另外，空数组也是返回 `true`。（空数组中所有元素都符合给定的条件，注：因为空数组没有元素）
  - `every` 不会改变原数组

```javascript
var arr = [1, 2, 3, 4, 5];
arr.every(function(x){
    return x < 10;
});	// true
arr.every(function(x){
    return x < 3;
});	// false
```

#### `Array.prototype.filter()`

`filter()` 方法检测数组元素，并返回符合条件的所有元素的数组。

```javascript
var new_array = arr.filter(function callback(currentValue, index, arr), thisValue);
```

- 参数：
  - `callback`：用来测试数组每个元素的函数。返回 `true` 表示保留该元素（通过测试），为 `false` 则不保留。它接受三个参数：
    - `currentValue`：数组中当前正在处理的元素的值
    - `index` `可选` ：数组中当前正在处理的元素的索引
    - `array` `可选` ：调用了 `filter` 的数组
  - `thisArg` `可选` ：执行 `callback` 函数时使用的 `this` 值
- 返回值：一个新的通过测试的元素集合的数组，如果没有元素通过测试则返回空数组
- 描述：
  - `filter` 为数组中的每个元素调用一次 `callback` 函数，并利用所有使得 `callback` 返回 `true` 或 [等价于 true 的值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) 的元素创建一个新数组。`callback` 只会在已经赋值的索引上被调用，对于那些已经被删除或者从未被赋值的索引不会被调用。那些没有通过 `callback` 测试的元素会被跳过，不会被包含在新数组中
  - `callback` 被调用时传入三个参数：元素的值，元素索引，以及被遍历的数组
  - 如果为 `filter` 提供一个 `thisArg` 参数，则它会被作为 `callback` 被调用时的 `this` 值。否则，`callback` 的 `this` 值在非严格模式下将是全局对象，严格模式下为 `undefined`
  - `filter` 遍历的元素范围在第一次调用 `callback` 之前就已经确定了。在调用 `filter` 之后被添加到数组中的元素不会被 `filter` 遍历到。如果已经存在的元素被改变了，则他们传入 `callback` 的值是 `filter` 遍历到它们那一刻的值。被删除或从来未被赋值的元素不会被遍历到
  - `filter` 不会改变原数组，它返回过滤后的新数组

```javascript
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
arr.filter(function(x, index){
    return index % 3 === 0 || x >= 8;
});	// returns [1, 4, 7, 8, 9, 10]
arr;	// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

#### `Array.prototype.forEach()`

`forEach()` 方法对数组的每个元素执行执行一次指定函数。可用于数组遍历。

```javascript
array.forEach(callback[, thisArg]);

array.forEach(callback(currentValue, index, array){
	// do something              
}, this)
```

- 参数：
  - `callback`：为数组中每个元素执行的函数，该函数接收三个参数：
    - `currentValue`：数组中当前正在处理的元素的值
    - `index` `可选` ：数组中当前正在处理的元素的索引
    - `array` `可选` ：`forEach()` 方法正在操作的数组
  - `thisArg` `可选` ：执行回调函数时用作 `this` 的值（参考对象）
- 返回值：`undefined`
- 描述：
  - `forEach` 方法按升序为数组中含有有效值的每一项执行一次 `callback` 函数，那些已删除或者未初始化的项将被跳过（如，操作稀疏数组时）
  - `callback` 函数会被依次传入三个参数：
    - 数组当前项的值
    - 数组当前项的索引
    - 数组对象本身
  - 如果给 `forEach` 传递了 `thisArg` 参数，当调用时，它将被传给 `callback` 函数，作为它的 `this` 值。否则，将会传入 `undefined` 作为它的 `this` 值。
  - `forEach` 遍历的范围在第一次调用 `callback` 前就会确定。调用 `forEach` 后添加到数组中的项不会被 `callback` 访问到。如果已经存在的值被改变，则传递给 `callback` 的值是 `forEach` 遍历到它们那一刻的值。已删除的项不会被遍历到。如果已访问的元素在迭代时被删除了，之后的元素将被跳过。
  - `forEach()` 为每个数组元素执行 `callback` 函数，不同于 `map()` 或 `reduce()`，它总是返回 `undefined` 值，并且不可链式调用

```javascript
var arr = [1, 2, 3, 4, 5];
arr.forEach(function(x, index, a){
    console.log(x + '|' + index + '|' + (a === arr));
});
// 1|0|true
// 2|1|true
// 3|2|true
// 4|3|true
// 5|4|true
```

#### `Array.prototype.indexOf()`

`indexOf（）` 方法返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回 -1。

```javascript
arr.indexOf(searchElement);
arr.indexOf(searchElement[, fromIndex=0])
```

- 参数：
  - `searchElement`：要查找的元素
  - `fromIndex`：开始查找的位置
    - 如果该索引值大于或等于数组长度，意味着不会在数组里查找，返回 -1
    - 如果参数中提供的索引值是一个负值，则将其作为数组末尾的一个抵消，即 -1 表示从最后一个元素开始查找，-2 表示从倒数第二个元素开始查找 ，以此类推
    - 注意：如果参数中提供的索引值是一个负值，并不改变其查找顺序，查找顺序仍然是从前向后查询数组。如果抵消后的索引值仍小于0，则整个数组都将会被查询。其默认值为0
- 返回值：首个被找到的元素在数组中的索引位置，若没有找到则返回 -1
- 描述：
  - `indexOf` 使用 strict equality（无论是 ===, 还是 triple-equals 操作符都基于同样的方法）进行判断 `searchElement` 与数组中包含的元素之间的关系

```javascript
var arr = [1, 2, 3, 2, 1];
arr.indexOf(2);	// 1
arr.indexOf(99);	// -1
arr.indexOf(1, 1);	// 4
arr.indexOf(1, -3);	// 4
arr.indexOf(2, -1);	// -1
```

#### `Array.prototype.join()`

`join()` 方法将一个数组（或一个类数组对象）的所有元素连接成一个字符串并返回这个字符。

```javascript
str = arr.join();
// 默认为 ","

str = arr.join("");
// 分隔符 === 空字符串 ""

str = arr.join(separator);
// 分隔符
```

- 参数：
  - `separator`：指定一个字符串来分割数组的每个元素
    - 必要时，分隔符 `separator` 将被转换为字符串
    - 如果省略 `separator`，数组数组元素用逗号分隔。默认为 `,`
    - 如果 `separator` 是空字符串 (`“”`)，则所有元素将连接在一起，且它们之间没有任何字符
- 返回值：一个所有数组元素连接的字符串。
  - 如果 `arr.length` 为 0，则返回空字符串
- 注意：该方法不会改变数组

#### `Array.prototype.lastIndexOf()`

`lastIndexOf()` 方法返回指定元素（也即有效的 JavaScript 值或变量）在数组中的最后一个的索引，如果不存在则返回 -1。

```javascript
arr.lastIndexOf(searchElement[, fromIndex])
```

- 参数：
  - `searchElement`：被查找的元素
  - `fromIndex`：从此位置开始，逆向查找
    - 默认为数组长度减一 `arr.length - 1`，即整个数组都被查找
    - 如果该值大于或等于数组的长度，则整个数组会被查找
    - 如果为负值，将其视为从数组末尾向前的偏移。即使该值为负，数组仍然会被从后向前查找。如果该值为负，且其绝对值大于数组长度，则返回 -1，即数组不会被查找
- 返回值：数组中最后一个元素的索引，如未找到返回 -1
- 描述：`lastIndexOf` 使用**严格相等**（strict equality，即 `===`）比较 `searchElement` 和数组中的元素

```javascript
var arr = [1, 2, 3, 2, 1];
arr.lastIndexOf(2);	// 3
arr.lastIndexOf(2, -2);	// 3
arr.lastIndexOf(2, -3);	// 1
```

#### `Array.prototype.map()`

`map()` 方法通过指定函数处理数中的每个元素，并返回处理后的数组。

```javascript
var new_array = array.map(function callback(currentValue, index, array), thisArg);
```

- 参数：
  - `callback` ：生成新数组元素的函数，使用三个参数：
    - `currentValue`：数组中当前正在处理的元素的值
    - `index` `可选` ：数组中当前正在处理的元素的索引
    - `array` `可选` ：`map` 方法被调用的数组
  - `thisArg` `可选` ：执行 `callback` 函数时使用的 `this` 值
- 返回值：一个新数组，每个元素都是回调函数的结果
- 描述：
  - `map` 方法会对原数组中的每个元素都按顺序调用一次 `callback` 函数。`callback` 每次执行后的返回值（包括 `undefined`）组合起来形成一个新数组。`callback` 函数只会在有值的索引上被调用，那些未被赋值或使用 `delete` 删除过的索引则不会调用
  - `callback` 函数传入三个参数：数组元素，元素索引，以及原数组本身
  - 如果 `thisArg` 参数有值，则每次 `callback` 函数被调用的时候，`this` 都会指向 `thisArg` 参数上的这个对象。如果省略了 `thisArg` 参数，或者赋值为 `null` 或 `undefined`，则 `this` 指向全局对象
  - `map()` 本身不修改调用它的原数组（但是可以在 `callback` 执行时改变原数组）
  - 使用 `map()` 方法处理数组时，数组元素的范围是在 `callback` 方法第一次调用之前就已经确定了的。在 `map` 方法执行的过程中，原数组中新增加的元素将不会被 `callback` 访问到；若已经存在的元素被改变了或删除了，则它们传递到 `callback` 的值是 `map` 方法遍历到它们那一刻的值；而被删除的元素将不会被访问到

```javascript
var arr = [1, 2, 3];
arr.map(function(x){
    return x + 10;
});	// [11, 12, 13]
arr;	// [1, 2, 3]
```

#### `Array.prototype.reduce()`
`reduce()` 方法对数组中的每个元素执行一个由您提供的 **reducer** 函数（升序执行），将其结果汇总为单个返回值。

```javascript
array.reduce(function callback(accumulator, currentValue, currentIndex, array), initialValue);
```

- 参数：
  - `callback`：为数组中每个元素执行的函数，包含四个参数：
    - `accumulator`：累计器累计回调的返回值。它是先前在回调中返回的累积值，或 `initialValue`
    - `currentValue`：数组中当前正在处理的元素的值
    - `currentIndex` `可选`：数组中当前正在处理的元素的索引
    - `array` `可选`：调用了 `reduce()` 的数组
  - `initialValue` `可选`：作为第一次调用 `callback` 函数时第一个参数的值。如果没有提供初始值，则将使用
- 返回值：函数累积处理的结果
- 描述：
  - `reduce`为数组中的每一个元素依次执行`callback`函数，不包括数组中被删除或从未被赋值的元素，接受四个参数：
    - `accumulator` 累计器
    - `currentValue` 当前值
    - `currentIndex` 当前索引
    - `array` 数组
  - 回调函数第一次执行时，`accumulator` 和 `currentValue`的取值有两种情况：如果调用`reduce()` 时提供了 `initialValue`，`accumulator` 取值为 `initialValue`，`currentValue` 取数组中的第一个值；如果没有提供 `initialValue`，那么 `accumulator` 取数组中的第一个值，`currentValue` 取数组中的第二个值<br>**注意：**如果没有提供`initialValue`，`reduce` 会从索引 1 开始执行 `callback` 方法，跳过第一个索引（索引 0）。如果提供 `initialValue`，则从索引 0 开始
  - 如果数组为空且没有提供 `initialValue`，会抛出 `TypeError` 。如果数组仅有一个元素（无论位置如何）并且没有提供 `initialValue`， 或者有提供 `initialValue` 但是数组为空，那么此唯一值将被返回并且 `callback` 不会被执行
  - `reduce` 不会改变原数组

```javascript
var arr = [1, 2, 3];
var sum = arr.reduce(function(x, y){
    return x + y;
}, 0);	// 6
arr;	// [1, 2, 3]

arr = [3, 9, 6];
var max = arr.reduce(function(x, y){
    console.log(x + '|' + y);
    return x > y ? x : y;
});
// 3|9
// 9|6
max;	// 9
```

#### `Array.prototype.reduceRight()`

`reduceRight()` 方法类似于 `reduce()` 方法，不过是降序执行的（从右到左）。

```javascript
array.reduceRight(function callback(previousValue, currentValue, currentIndex, array), initialValue);
```

- 参数：
  - `callback`：为数组中每个元素执行的函数，包含四个参数：
    - `previousValue`：累计器累计回调的返回值。它是先前在回调中返回的累积值，或 `initialValue`
    - `currentValue`：数组中当前正在处理的元素的值
    - `currentIndex` `可选`：数组中当前正在处理的元素的索引
    - `array` `可选`：调用了 `reduce()` 的数组
  - `initialValue` `可选`：作为第一次调用 `callback` 函数时第一个参数的值。如果没有提供初始值，则将使用
- 返回值：函数累积处理的结果
- 描述：
  - `reduceRight` 为数组中每个元素调用一次 `callback` 回调函数，但是数组中被删除的索引或从未被赋值的索引会跳过。回调函数接受四个参数：初始值（或上次调用回调的返回值）、当前元素值、当前索引，以及调用 `reduceRight` 的数组。
  - 回调函数第一次执行时，`previousValue` 和 `currentValue`的取值有两种情况：如果调用`reduceRight()` 时提供了 `initialValue`，则 `previousValue` 取值为 `initialValue`，`currentValue` 取数组中的最后一个元素的值；如果没有提供 `initialValue`，那么 `previousValue` 取数组中的最后一个值，`currentValue` 取数组中的倒数第二个值<br>
  - 如果数组为空且没有提供 `initialValue`，会抛出 `TypeError` 。如果数组仅有一个元素（无论位置如何）并且没有提供 `initialValue`， 或者有提供 `initialValue` 但是数组为空，那么此唯一值将被返回并且 `callback` 不会被执行
  - `reduceRight` 不会改变原数组

```javascript
var arr = [3, 9, 6];
var max = arr.reduceRight(function(x, y){
    console.log(x + '|' + y);
    return x > y ? x : y;
});
// 6|9
// 9|3
max;	// 9
```

#### `Array.prototype.reverse()`

`reverse()` 方法将数组中元素的位置颠倒

```javascript
arr.reverse();
```

- 参数：无
- 描述：`reverse()` 方法颠倒数组中元素的位置，并返回该数组的引用


#### `Array.prototype.slice()`

`slice()` 方法返回一个新的数组对象，这一对象是一个由 `begin` 和 `end`（不包括 `end`）决定的原始数组的**浅拷贝**。原始数组不会改变。

```javascript
arr.slice();	
// [0, end]

arr.slice(begin);	
// [begin, end]

arr.slice(begin, end);
// [begin, end)
```

- 参数：
  - `begin` `可选`：从索引处开始提取原数组中的元素
    - 如果该参数为复数，则表示从原数组中的倒数第几个元素开始提取。如：`slice(-2)` 表示提取原数组中的倒数第二个元素到最后一个元素（包含最后一个元素）
    - 如果省略 `begin`，则 `slice` 从索引 0 开始
  - `end` `可选`：在该索引处结束提取原数组元素
    - `slice` 会提取原数组中从 `begin` 到 `end` 的所有元素（包含 `begin` 但不包含 `end`）
    - 如果该参数为负数，则他表示在原数组中的倒数第几个元素结束抽取。如：`slice(-2, -1)` 表示抽取了原数组中的倒数第二个元素到最后一个元素（不包含最后一个元素，即只有倒数第二个元素）
    - 如果 `end` 被省略，则 `slice` 会一直提取到原数组末尾
    - 如果 `end` 大于数组长度，`slice` 也会一直提取到原数组末尾
- 返回值：一个含有提取元素的新数组
- 描述：
  - `slice` 不修改原数组，只会返回一个浅复制了原数组中的元素的一个新数组。原数组中的元素会按照下述规则拷贝：
    - 如果该元素是一个对象引用（不是实际的对象），`slice` 会拷贝这个对象引用到新的数组里。两个对象引用都引用了同一个对象。如果被引用的对象发生改变，则新的和原来的数组中这个元素也会发生改变
    - 对于字符串、数字及布尔值来说（不是 `String`、`Number` 或 `Boolean` 对象），`slice`会拷贝这些值到新的数组里。在别的数组里修改这些字符串、数字或布尔值，不会影响另一个数组
  - 向新数组中添加或删除元素，原数组不会受到影响，反之亦然

```javascript
var arr = [1, 2, 3, 4, 5];
arr.slice(1, 3);	// [2, 3]
arr.slice(1);	// [2, 3, 4, 5]
arr.slice(1, -1);	// [2, 3, 4]
arr.slice(-4, -3);	// [2]
```

#### `Array.prototype.some()`
`some()` 方法检测数组元素中是否有元素符合指定条件。

```javascript
array.some(function callback(currentValue, index, array), thisArg);
```

- 参数：
  - `callback`：用来测试每个元素的函数，接受三个参数：
    - `element`：数组中当前正在处理的元素的值
    - `index` `可选` ：数组中当前正在处理的元素的索引
    - `array` `可选` ：调用了 `some()` 的数组
  - `thisArg` `可选` ：执行 `callback` 函数时使用的 `this` 值
- 返回值：布尔值。如果数组中有元素满足条件返回 `true`，否则返回 `false`
- 描述：
  - `some()` 为数组中的每一个元素执行一次 `callback` 函数，直到找到一个使得 `callback` 返回一个“真值”（即可转换为布尔值 `true` 的值）。如果找到了这样一个值，`some()` 将会立即返回 `true`。否则，`some()` 返回 `false`。`callback` 只会在那些”有值“的索引上被调用，不会在那些被删除或从未被赋值的索引上调用
  - `callback` 被调用时传入三个参数：元素的值，元素的索引，被遍历的数组
  - 如果为 `every` 提供一个 `thisArg` 参数，则该参数为调用 `callback` 时的 `this` 值。如果省略该参数，则 `callback` 被调用时的 `this` 值，在非严格模式下为全局对象，在严格模式下传入 `undefined`
  - `some()` 遍历的元素的范围在第一次调用 `callback`. 时就已经确定了。在调用 `some()` 后被添加到数组中的值不会被 `callback` 访问到。如果数组中存在且还未被访问到的元素被 `callback`改变了，则其传递给 `callback` 的值是 `some()` 访问到它那一刻的值
  - `some()` 被调用时不会改变数组

```javascript
var arr = [1, 2, 3, 4, 5];
arr.some(function(x){
    return x === 3;
});	// true
arr.some(function(x){
    return x === 100;
});	// false
```

#### `Array.prototype.sort()`

`sort()` 方法用[原地算法](https://en.wikipedia.org/wiki/In-place_algorithm)对数组的元素进行排序，并返回数组（原数组被修改）。

```javascript
arr.sort(compareFunction);
```

- 参数：
  - `compareFunction` `可选`：用于指定按某种顺序进行排列的函数。如果省略，则将每个元素转换为字符串，根据其 Unicode 位点值进行排序。
- 返回值：排序后的数组（数组已原地排序，并且不进行复制）。
- 注意：
  - 如果没有指明 `compareFunction` ，那么元素会按照转换为的字符串的诸个字符的Unicode位点进行排序。例如 "Banana" 会被排列到 "cherry" 之前。当数字按由小到大排序时，9 出现在 80 之前，但因为（没有指明 `compareFunction`），比较的数字会先被转换为字符串，所以在Unicode顺序上 "80" 要比 "9" 要靠前
  - 如果指明了 `compareFunction` ，则根据该函数的返回值对所有 “非 `undefined`” 的数组元素进行排序（所有的 `undefined` 元素都排到数组的末尾，且不调用 `compareFunction`）。<br>如果 a 和 b 是两个被比较的元素，那么：
    - 当 `compareFunction(a, b)` 返回值小于 0 时，a 排在 b 之前（a 的索引低于 b）
    - 当 `compareFunction(a, b)` 返回值等于 0 时，a 和 b 相对位置不变
    - 当 `compareFunction(a, b)` 返回值大于 0 时，a 排在 b 之后
  - `sort()` 方法取决于具体实现，因此无法保证排序的时间和空间复杂性

```javascript
var arr = [13, 24, 51, 3];
arr.sort();	// [13, 24, 3, 51]
arr;	// [13, 24, 3, 51]

arr.sort(function(a, b){
    return a - b;
});	// [3, 13, 24, 51]

var arr1 = [{age: 25}, {age: 39}, {age: 99}];
arr.sort(function(a, b){
    return a.age - b.age;
});
arr.forEach(function(item){	// forEach() 遍历
    console.log('age', item.age);
});
// RESULT:
// age 25
// age 39
// age 99
```

#### `Array.prototype.splice()`

`splice()` 方法通过删除现有元素 / 添加新元素来修改数组。并以数组形式返回原数组中被修改的内容

```javascript
array.splice(start[, deleteCount[, item1[, item2[, ...]]]])
```

- 参数：
  - `start`：指定修改的开始位置（从 0 计数）
    - 如果超过了数组的长度，则从数组末尾开始添加内容
    - 如果是负值，则表示从数组倒数第几个元素开始
    - 如果负数的绝对值大于数组的长度，则表示开始位置为第 0 位
  - `deleteCount` `可选`：整数，表示要移除的数组元素的个数
    - 如果 `deleteCount` 是 0 或负数，则不移除元素。这种情况下，至少应（在 `start` 位之前）添加一个新元素
    - 如果 `deleteCount` 大于 `start` 之后的元素的总数，则从 `start` 开始，之后的元素豆浆杯删除（含第 `start` 位）
    - 如果省略 `deleteCount`，则其相当于 `arr.length - start`
  - `item1, item2, ...` `可选`：要添加进数组的元素，从 `start` 的位置开始。如果未指定，则 `splice()` 将只删除数组元素
- 返回值：由被删除的元素组成的一个数组
  - 如果只删除了一个元素，则返回只包含一个元素的数组
  - 如果没有删除元素，则返回空数组
- 描述：
  - `splice()` 方法使用 `deleteCount` 参数来控制是删除还是添加元素
  - 如果添加进数组的元素个数不等于被删除的元素个数，数组的长度会发生相应的改变

```javascript
var arr = [1, 2, 3, 4, 5];
arr.splice(2);	// returns [3, 4, 5]
arr;	// [1, 2]，原数组被修改


arr = [1, 2, 3, 4, 5];
arr.splice(2, 2);	// returns [3, 4]
arr;	// [1, 2, 5]

arr = [1, 2, 3, 4, 5];
arr.splice(1, 1, 'b', 'c');	// returns [2]
arr;	// [1, "b", "c", 3, 4, 5]
arr.splice(1, 0, 'a');	// returns []
arr;	// [1, 'a', 'b', 'c', 3, 4, 5]
```

## 5.4 数组小结

`数组` 与 `一般对象`

| 相同                         | 不同                                              |
| ---------------------------- | ------------------------------------------------- |
| 都可以继承                   | 数组自动更新 length                               |
| 数组是对象，对象不一定是数组 | 按索引访问数组常常比访问一般对象属性明显迅速      |
| 都可以当做对象添加、删除属性 | 数组对象继承 Array.prototype 上的大量数组操作方法 |