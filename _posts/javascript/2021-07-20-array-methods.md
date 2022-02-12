---
layout:     post
title:      "JavaScript 数组方法对比整理"
subtitle:   "Comparison of array methods"
date:       2021-07-20
author:     "aladingzl"
header-style: text
tags:
    - JavaScript
    - 笔记
---

### 改变原数组的：

1、 **shift**：将第一个元素删除并且返回删除元素，空即为 undefined

```javascript
let a = arr.shift()
console.log(a)         // a
console.log(arr)       // ['b', 'c', 'd']
```

2、**unshift**：向数组开头添加元素，并返回新的长度

```javascript
let a = arr.unshift(0)
console.log(a)        // 5 返回数组长度
console.log(arr)      // [0, 'a', 'b', 'c', 'd']
```

3、**pop**：删除最后一个并返回删除的元素

```javascript
let a = arr.pop()
console.log(a)        // d
console.log(arr)      // ['a', 'b', 'c']
```

4、**push**：向数组末尾添加元素，并返回新的长度

```javascript
let a = arr.push('f')
console.log(a)        // 5 返回数组长度
console.log(arr)      // ['a', 'b', 'c', 'd', 'f']
var vegetables = ['parsnip', 'potato'];
var moreVegs = ['celery', 'beetroot'];
```

拓展

```javascript
// 将第二个数组融合进第一个数组
// 相当于 vegetables.push('celery', 'beetroot');
Array.prototype.push.apply(vegetables, moreVegs);

console.log(vegetables);
// ['parsnip', 'potato', 'celery', 'beetroot']
var obj = {
    length: 0,

    addElem: function addElem (elem) {
        // obj.length is automatically incremented
        // every time an element is added.
        [].push.call(this, elem);
    }
};

// Let's add some empty objects just to illustrate.
obj.addElem({});
obj.addElem({});
console.log(obj.length);
// → 2
```

尽管 obj 不是数组，但是 push 方法成功地使 obj 的 length 属性增长了，就像我们处理一个实际的数组一样。

5、**reverse**：颠倒数组顺序

```javascript
let a = arr.reverse()
console.log(a)        // ["d", "c", "b", "a"]
console.log(arr)      // ["d", "c", "b", "a"]
```

6、**sort**：对数组排序

```javascript
let arr = ['c', 'a', 'd', 'b']
let a = arr.sort()
console.log(a)        // ['a', 'b', 'c', 'd']
console.log(arr)      // ['a', 'b', 'c', 'd']
```

7、**splice**: 参数 (start,length,item) 删，增，替换数组元素，返回被删除数组，无删除则不返回

```javascript
let a = arr.splice(1, 2, 'f')
console.log(a)        // 返回被删除的元素数组['b', 'c']
console.log(arr)      // 在添加的地方添加元素后的数组["a", "f", "d"]
```

8、**copyWithin**:参数 (target, start, end) 浅复制数组的一部分到同一数组中的另一个位置，并返回它，不会改变原数组的长度。

```javascript
let a = arr.copyWithin(1, 2, 3)
console.log(a)  //返回被复制的元素数组 ['a', 'c', 'c', 'd']
console.log(arr)  //原元素数组已经改变 ['a', 'c', 'c', 'd']
```

9、**fill**:用一个元素填充原来的数组

```javascript
let a = arr.fill('e', 2, 4);
console.log(a) // 返回它会改变调用它的 `this` 对象本身, 然后返回它['a', 'b', 'e', 'e']
console.log(arr) // ['a', 'b', 'e', 'e']
```

### 不改变原数组的：

1、**concat**：targetArr.concat(otherArr[,anyOtherArr])连接多个数组，返回新的数组

```javascript
let a = arr.concat(['e', 'f'])
console.log(a)        // 新数组 ["a", "b", "c", "d", "e", "f"]
console.log(arr)      // ["a", "b", "c", "d"] 不变
```

2、**join**：将数组中所有元素以参数作为分隔符放入一个字符

```javascript
let a = arr.join('-')
console.log(a)        // 字符串 a-b-c-d
console.log(arr)      // ["a", "b", "c", "d"] 不变
```

3、**slice**：slice(start,end)，返回选定元素

```javascript
let a = arr.slice(1)
console.log(a)        // ["b", "c", "d"]
console.log(arr)      // ["a", "b", "c", "d"] 不变
```

4、**map**：创建一个**新数组**，其结果是该数组中的每个元素是调用一次提供的函数后的返回值。

```javascript
map(function callback(currentValue[, index[, array]]) {
 // Return element for new_array 
}[, thisArg])
```

> map() 不修改调用它的原数组本身（当然可以在 `callback` 执行时改变原数组）
> map() 不会对空数组进行检测。
> map() 方法返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。
> map() 方法按照原始数组元素顺序依次处理元素。

**实际应用中**

```javascript
// 1、基本数据类型
let arr = [1, 2, 3, 4, 5]
let newArr = arr.map((item) => item * 2)
console.log(arr); // [1,2,3,4,5]
console.log(newArr); //[2, 4, 6, 8, 10]

// 2、引用数据类型
let arr = [{
  name: 'shandong'
}, {
  name: 'chenggdu'
}]
let newArr = arr.map((item) => {
  item.belongTo = 'china';
  return item;
})
console.log(arr);
// [{name: "shandong", belongTo: "china"},{name: "chengdu", belongTo: "china"}]
console.log(newArr);
// [{name: "shandong", belongTo: "china"},{name: "chengdu", belongTo: "china"}]
```

5、**filter, forEach, some, every, reduce**等不改变原数组

**reduce** 函数接收参数

- 

1. 

- 

- Accumulator (acc) (累计器)
- Current Value (cur) (当前值)
- Current Index (idx) (当前索引)
- Source Array (src) (源数组)

**Link**

[JavaScript 数组遍历方法的对比](https://juejin.cn/post/6844903538175262734)

