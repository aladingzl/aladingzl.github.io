---
layout:     post
title:      "JS 取整方法整理"
subtitle:   ""
date:       2021-09-09
author:     "aladingzl"
header-style: text
tags:
    - JavaScript
    - 笔记
---



### 数字取整方法

#### 1. parseInt()

```js
// js内置函数，注意接受参数是string，所以调用该方法时存在类型转换
parseInt("1.5555") // => 1
```

#### 2. Number.toFixed(0)

```js
// 注意toFixed返回的字符串，若想获得整数还需要做类型转换
1.5555.toFixed(0)  // => "1"
```

#### 3. Math.ceil()

```js
// 向上取整
Math.ceil(1.5555) // => 2
```

#### 4. Math.floor()

```js
// 向下取整
Math.floor(1.5555) // => 1
```

#### 5. Math.round()

```js
// 四舍五入取整
Math.round(1.5555) // => 2

Math.round(1.4999) // => 1
```

#### 6. Math.trunc()

```js
// 舍弃小数取整
Math.trunc(1.5555) // => 1
```

#### 7. 双按位非取整

```js
// 利用位运算取整，仅支持32位有符号整型数，小数位会舍弃，下同
~~1.5555 // => 1
```

#### 8. 按位运或取整

```js
1.5555 | 0  // => 1
```

#### 9. 按位异或取整

```js
1.5555 ^ 0  // => 1
```

#### 10. 左移0位取整

```js
1.5555 << 0 // => 1
```

按位运算符方法处理比较大的数字时（当数字范围超出 ±2^31−1 即 ±2147483647 即 2.147483647e9），会有一些异常情况。 例如：

```javascript
console.log(20.15 | 0);  // -> 20
console.log((-20.15) | 0);  // -> -20
// 异常情况
console.log(3000000000.15 | 0);  // ->  -1294967296
```



### 运算符

| 运算符             | 用　　法  | 描述                                                         |
| :----------------- | :-------: | :----------------------------------------------------------- |
| 按位与（AND）      |  `a & b`  | 对于每一个比特位，只有两个操作数相应的比特位都是 1 时，结果才为 1，否则为 0。 |
| 按位或（OR）       |  `a | b`  | 对于每一个比特位，当两个操作数相应的比特位至少有一个 1 时，结果为 1，否则为 0。 |
| 按位异或（XOR）    |  `a ^ b`  | 对于每一个比特位，当两个操作数相应的比特位有且只有一个 1 时，结果为 1，否则为 0。 |
| 按位非（NOT）      |   `~a`    | 反转操作数的比特位，即 0 变成 1，1 变成 0。                  |
| 左移（Left shift） | `a << b`  | 将 a 的二进制形式向左移 b (< 32) 比特位，右边用 0 填充。     |
| 有符号右移         | `a >> b`  | 将 a 的二进制表示向右移 b (< 32) 位，丢弃被移出的位。        |
| 无符号右移         | `a >>> b` | 将 a 的二进制表示向右移 b (< 32) 位，丢弃被移出的位，并使用 0 在左侧填充。 |

### 位运算用途整理

使用 `&` 运算符判断一个数的奇偶

```javascript
// 偶数 & 1 = 0
// 奇数 & 1 = 1
console.log(2 & 1)    // 0
console.log(3 & 1)    // 1
```

使用 `~, >>, <<, >>>, |` 来取整

```javascript
console.log(~~ 6.83)    // 6
console.log(6.83 >> 0)  // 6
console.log(6.83 << 0)  // 6
console.log(6.83 | 0)   // 6
// >>>不可对负数取整
console.log(6.83 >>> 0)   // 6
```

使用 `^` 来完成值交换

```javascript
var a = 5
var b = 8
a ^= b
b ^= a
a ^= b
console.log(a)   // 8
console.log(b)   // 5
```

使用 `&, >>, |`来完成 rgb 值和16 进制颜色值之间的转换

```javascript
/**
 * 16进制颜色值转RGB
 * @param  {String} hex 16进制颜色字符串
 * @return {String}     RGB颜色字符串
 */
  function hexToRGB(hex) {
    var hexx = hex.replace('#', '0x')
    var r = hexx >> 16
    var g = hexx >> 8 & 0xff
    var b = hexx & 0xff
    return `rgb(${r}, ${g}, ${b})`
}

/**
 * RGB颜色转16进制颜色
 * @param  {String} rgb RGB进制颜色字符串
 * @return {String}     16进制颜色字符串
 */
function RGBToHex(rgb) {
    var rgbArr = rgb.split(/[^\d]+/)
    var color = rgbArr[1]<<16 | rgbArr[2]<<8 | rgbArr[3]
    return '#'+ color.toString(16)
}
// -------------------------------------------------
hexToRGB('#ffffff')               // 'rgb(255,255,255)'
RGBToHex('rgb(255,255,255)')      // '#ffffff'
```

