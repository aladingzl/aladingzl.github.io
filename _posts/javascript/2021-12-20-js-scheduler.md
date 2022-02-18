---
layout:     post
title:      "JS实现一个带并发限制的异步调度器Scheduler"
subtitle:   "Implementing an asynchronous scheduler with concurrency limits"
date:       2021-12-20
author:     "aladingzl"
header-style: text
tags:
    - JavaScript
    - 手撕代码
---
### 题目要求

```javascript
// JS实现一个带并发限制的异步调度器Scheduler，
// 保证同时运行的任务最多有两个。
// 完善代码中Scheduler类，
// 使得以下程序能正确输出

class Scheduler {
	constructor() {
		this.count = 2
		this.queue = []
		this.run = []
	}

	add(task) {
                 // ...
	}
}

const timeout = (time) => new Promise(resolve => {
	setTimeout(resolve, time)
})

const scheduler = new Scheduler()
const addTask = (time, order) => {
	scheduler.add(() => timeout(time)).then(() => console.log(order))
}

addTask(1000, '1')
addTask(500, '2')
addTask(300, '3')
addTask(400, '4')
// output: 2 3 1 4

// 一开始，1、2两个任务进入队列
// 500ms时，2完成，输出2，任务3进队
// 800ms时，3完成，输出3，任务4进队
// 1000ms时，1完成，输出1
// 1200ms时，4完成，输出4

```

### 代码实现及思路

```javascript
class Scheduler {
  constructor(limit) {
    // 最大可并发任务数
    this.limit = limit;
    // 当前并发任务数
    this.count = 0;
    // 阻塞的任务队列
    this.queue = [];
  }

  async add(fn) {
    if (this.count >= this.limit) {
      // 
      await new Promise(resolve => this.queue.push(resolve));
    }
    this.count++;

    let res = await fn();

    this.count--;

    if (this.queue.length) {
      this.queue.shift()();
    } 
    // 返回函数执行的结果
    return res;
  }
}

let schedule = new Scheduler(2);

function asyncFactory(n, time) {
  return function () {
    return new Promise(resolve => {
      setTimeout(() => resolve(n), time);
    })
  }
}

schedule.add(asyncFactory(1, 1000)).then((n) => {
  console.log(`异步任务:${n}`)
});
schedule.add(asyncFactory(2, 500)).then((n) => {
  console.log(`异步任务:${n}`)
});
schedule.add(asyncFactory(3, 300)).then((n) => {
  console.log(`异步任务:${n}`)
});
schedule.add(asyncFactory(4, 400)).then((n) => {
  console.log(`异步任务:${n}`)
});
schedule.add(asyncFactory(5, 200)).then((n) => {
  console.log(`异步任务:${n}`)
});
schedule.add(asyncFactory(6, 2000)).then((n) => {
  console.log(`异步任务:${n}`)
});
//2,3,1,5,4,6
```



**Link**

https://github.com/Yuanyuanyuanc/aYuan-learning-notes/issues/2