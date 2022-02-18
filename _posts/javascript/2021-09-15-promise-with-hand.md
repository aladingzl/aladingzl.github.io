---
layout:     post
title:      "手动实现一个Promise"
subtitle:   "Promise"
date:       2021-09-15
author:     "aladingzl"
header-style: text
tags:
    - JavaScript
    - Promise
---

### 开始

### 搭建骨架

```javascript
//声明构造函数
function Promise(executor){
    //resolve 函数
    function resolve(data){}
    //reject 函数
    function reject(data){}
    //同步调用『执行器函数』
    executor(resolve, reject);
}
//添加 then 方法
Promise.prototype.then = function(onResolved, onRejected){}

//添加 catch 方法
Promise.prototype.catch = function(onRejected){}

//添加 resolve 方法
Promise.resolve = function(){}

//添加 reject 方法
Promise.reject = function(){}

//添加 all 方法
Promise.all = function(){}

//添加 race 方法
Promise.race = function(){}
```

### resovle与reject实现

```javascript
function Promise(executor){
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    //保存实例对象的 this 的值
    const self = this;// self _this that
    //resolve 函数
    function resolve(data){
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'fulfilled';// resolved
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
    }
    //reject 函数
    function reject(data){
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'rejected';// 
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
    }

    //同步调用『执行器函数』
    executor(resolve, reject);
}
```

### thow抛出异常改变状态

```javascript
function Promise(executor){
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    const self = this;// self _this that
    function resolve(data){
        self.PromiseState = 'fulfilled';
        self.PromiseResult = data;
    }    
    function reject(data){
        self.PromiseState = 'rejected';
        self.PromiseResult = data;
    }
	  //通过trycatch
    try{
        executor(resolve, reject);
    }catch(e){
        reject(e);
    }
}
```

### Promise对象状态只能修改一次

```javascript
function Promise(executor){
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    const self = this;
    function resolve(data){
				//判断状态
        if(self.PromiseState !== 'pending') return;
        self.PromiseState = 'fulfilled';
        self.PromiseResult = data;
    }
    
    function reject(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        self.PromiseState = 'rejected';
        self.PromiseResult = data;
    }
    try{        
        executor(resolve, reject);
    }catch(e){        
        reject(e);
    }
}
```

### then方法执行回调

```javascript
//添加 then 方法
Promise.prototype.then = function(onResolved, onRejected){
    //调用回调函数  PromiseState
    if(this.PromiseState === 'fulfilled'){
        onResolved(this.PromiseResult);
    }
    if(this.PromiseState === 'rejected'){
        onRejected(this.PromiseResult);
    }
}
```

### 异步任务then回调的执行

```javascript
function Promise(executor){
		this.PromiseState = 'pending';
    this.PromiseResult = null;
    this.callback = {};
    const self = this;// self _this that
    function resolve(data){
        if(self.PromiseState !== 'pending') return;
        self.PromiseState = 'fulfilled';
        self.PromiseResult = data;
        //调用成功的回调函数
        if(self.callback.onResolved){
            self.callback.onResolved(data);
        }
    }
    //reject 函数
    function reject(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'rejected';// 
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //执行回调
        if(self.callback.onResolved){
            self.callback.onResolved(data);
        }
    }
    try{
        //同步调用『执行器函数』
        executor(resolve, reject);
    }catch(e){
        //修改 promise 对象状态为『失败』
        reject(e);
    }
}

//添加 then 方法
Promise.prototype.then = function(onResolved, onRejected){
    //调用回调函数  PromiseState
    if(this.PromiseState === 'fulfilled'){
        onResolved(this.PromiseResult);
    }
    if(this.PromiseState === 'rejected'){
        onRejected(this.PromiseResult);
    }
    //判断 pending 状态
    if(this.PromiseState === 'pending'){
        //保存回调函数
        this.callback = {
            onResolved: onResolved,
            onRejected: onRejected
        }
    }
}
```

### 指定多个回调的执行

```javascript
//声明构造函数
function Promise(executor){
    //添加属性
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    //声明属性
    this.callbacks = [];
    //保存实例对象的 this 的值
    const self = this;// self _this that
    //resolve 函数
    function resolve(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'fulfilled';// resolved
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //调用成功的回调函数
        self.callbacks.forEach(item => {
            item.onResolved(data);
        });
    }
    //reject 函数
    function reject(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'rejected';// 
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //执行失败的回调
        self.callbacks.forEach(item => {
            item.onRejected(data);
        });
    }
    try{
        //同步调用『执行器函数』
        executor(resolve, reject);
    }catch(e){
        //修改 promise 对象状态为『失败』
        reject(e);
    }
}

//添加 then 方法
Promise.prototype.then = function(onResolved, onRejected){
    //调用回调函数  PromiseState
    if(this.PromiseState === 'fulfilled'){
        onResolved(this.PromiseResult);
    }
    if(this.PromiseState === 'rejected'){
        onRejected(this.PromiseResult);
    }
    //判断 pending 状态
    if(this.PromiseState === 'pending'){
        //保存回调函数
        this.callbacks.push({
            onResolved: onResolved,
            onRejected: onRejected
        });
    }
}
```

### 同步修改状态then方法执行回调

```javascript
//声明构造函数
function Promise(executor){
    //添加属性
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    //声明属性
    this.callbacks = [];
    //保存实例对象的 this 的值
    const self = this;// self _this that
    //resolve 函数
    function resolve(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'fulfilled';// resolved
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //调用成功的回调函数
        self.callbacks.forEach(item => {
            item.onResolved(data);
        });
    }
    //reject 函数
    function reject(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'rejected';// 
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //执行失败的回调
        self.callbacks.forEach(item => {
            item.onRejected(data);
        });
    }
    try{
        //同步调用『执行器函数』
        executor(resolve, reject);
    }catch(e){
        //修改 promise 对象状态为『失败』
        reject(e);
    }
}

//添加 then 方法
Promise.prototype.then = function(onResolved, onRejected){
    return new Promise((resolve, reject) => {
        //调用回调函数  PromiseState
        if(this.PromiseState === 'fulfilled'){
            try{
                //获取回调函数的执行结果
                let result = onResolved(this.PromiseResult);
                //判断
                if(result instanceof Promise){
                    //如果是 Promise 类型的对象
                    result.then(v => {
                        resolve(v);
                    }, r=>{
                        reject(r);
                    })
                }else{
                    //结果的对象状态为『成功』
                    resolve(result);
                }
            }catch(e){
                reject(e);
            }
        }
        if(this.PromiseState === 'rejected'){
            onRejected(this.PromiseResult);
        }
        //判断 pending 状态
        if(this.PromiseState === 'pending'){
            //保存回调函数
            this.callbacks.push({
                onResolved: onResolved,
                onRejected: onRejected
            });
        }
    })
}
```

### 异步修改状态then方法执行回调

```javascript
//声明构造函数
function Promise(executor){
    //添加属性
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    //声明属性
    this.callbacks = [];
    //保存实例对象的 this 的值
    const self = this;// self _this that
    //resolve 函数
    function resolve(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'fulfilled';// resolved
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //调用成功的回调函数
        self.callbacks.forEach(item => {
            item.onResolved(data);
        });
    }
    //reject 函数
    function reject(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'rejected';// 
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //执行失败的回调
        self.callbacks.forEach(item => {
            item.onRejected(data);
        });
    }
    try{
        //同步调用『执行器函数』
        executor(resolve, reject);
    }catch(e){
        //修改 promise 对象状态为『失败』
        reject(e);
    }
}

//添加 then 方法
Promise.prototype.then = function(onResolved, onRejected){
    const self = this;
    return new Promise((resolve, reject) => {
        //调用回调函数  PromiseState
        if(this.PromiseState === 'fulfilled'){
            try{
                //获取回调函数的执行结果
                let result = onResolved(this.PromiseResult);
                //判断
                if(result instanceof Promise){
                    //如果是 Promise 类型的对象
                    result.then(v => {
                        resolve(v);
                    }, r=>{
                        reject(r);
                    })
                }else{
                    //结果的对象状态为『成功』
                    resolve(result);
                }
            }catch(e){
                reject(e);
            }
        }
        if(this.PromiseState === 'rejected'){
            onRejected(this.PromiseResult);
        }
        //判断 pending 状态
        if(this.PromiseState === 'pending'){
            //保存回调函数
            this.callbacks.push({
                onResolved: function(){
                    try{
                        //执行成功回调函数
                        let result = onResolved(self.PromiseResult);
                        //判断
                        if(result instanceof Promise){
                            result.then(v => {
                                resolve(v);
                            }, r=>{
                                reject(r);
                            })
                        }else{
                            resolve(result);
                        }
                    }catch(e){
                        reject(e);
                    }
                },
                onRejected: function(){
                    try{
                        //执行成功回调函数
                        let result = onRejected(self.PromiseResult);
                        //判断
                        if(result instanceof Promise){
                            result.then(v => {
                                resolve(v);
                            }, r=>{
                                reject(r);
                            })
                        }else{
                            resolve(result);
                        }
                    }catch(e){
                        reject(e);
                    }
                }
            });
        }
    })
}
```

### then方法完善

```javascript
//声明构造函数
function Promise(executor){
    //添加属性
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    //声明属性
    this.callbacks = [];
    //保存实例对象的 this 的值
    const self = this;// self _this that
    //resolve 函数
    function resolve(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'fulfilled';// resolved
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //调用成功的回调函数
        self.callbacks.forEach(item => {
            item.onResolved(data);
        });
    }
    //reject 函数
    function reject(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'rejected';// 
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //执行失败的回调
        self.callbacks.forEach(item => {
            item.onRejected(data);
        });
    }
    try{
        //同步调用『执行器函数』
        executor(resolve, reject);
    }catch(e){
        //修改 promise 对象状态为『失败』
        reject(e);
    }
}

//添加 then 方法
Promise.prototype.then = function(onResolved, onRejected){
    const self = this;
    return new Promise((resolve, reject) => {
        //封装函数
        function callback(type){
            try{
                //获取回调函数的执行结果
                let result = type(self.PromiseResult);
                //判断
                if(result instanceof Promise){
                    //如果是 Promise 类型的对象
                    result.then(v => {
                        resolve(v);
                    }, r=>{
                        reject(r);
                    })
                }else{
                    //结果的对象状态为『成功』
                    resolve(result);
                }
            }catch(e){
                reject(e);
            }
        }
        //调用回调函数  PromiseState
        if(this.PromiseState === 'fulfilled'){
            callback(onResolved);
        }
        if(this.PromiseState === 'rejected'){
            callback(onRejected);
        }
        //判断 pending 状态
        if(this.PromiseState === 'pending'){
            //保存回调函数
            this.callbacks.push({
                onResolved: function(){
                    callback(onResolved);
                },
                onRejected: function(){
                    callback(onRejected);
                }
            });
        }
    })
}
```

### catch方法 异常穿透与值传递

```javascript
//声明构造函数
function Promise(executor){
    //添加属性
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    //声明属性
    this.callbacks = [];
    //保存实例对象的 this 的值
    const self = this;// self _this that
    //resolve 函数
    function resolve(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'fulfilled';// resolved
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //调用成功的回调函数
        self.callbacks.forEach(item => {
            item.onResolved(data);
        });
    }
    //reject 函数
    function reject(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'rejected';// 
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //执行失败的回调
        self.callbacks.forEach(item => {
            item.onRejected(data);
        });
    }
    try{
        //同步调用『执行器函数』
        executor(resolve, reject);
    }catch(e){
        //修改 promise 对象状态为『失败』
        reject(e);
    }
}

//添加 then 方法
Promise.prototype.then = function(onResolved, onRejected){
    const self = this;
    //判断回调函数参数
    if(typeof onRejected !== 'function'){
        onRejected = reason => {
            throw reason;
        }
    }
    if(typeof onResolved !== 'function'){
        onResolved = value => value;
        //value => { return value};
    }
    return new Promise((resolve, reject) => {
        //封装函数
        function callback(type){
            try{
                //获取回调函数的执行结果
                let result = type(self.PromiseResult);
                //判断
                if(result instanceof Promise){
                    //如果是 Promise 类型的对象
                    result.then(v => {
                        resolve(v);
                    }, r=>{
                        reject(r);
                    })
                }else{
                    //结果的对象状态为『成功』
                    resolve(result);
                }
            }catch(e){
                reject(e);
            }
        }
        //调用回调函数  PromiseState
        if(this.PromiseState === 'fulfilled'){
            callback(onResolved);
        }
        if(this.PromiseState === 'rejected'){
            callback(onRejected);
        }
        //判断 pending 状态
        if(this.PromiseState === 'pending'){
            //保存回调函数
            this.callbacks.push({
                onResolved: function(){
                    callback(onResolved);
                },
                onRejected: function(){
                    callback(onRejected);
                }
            });
        }
    })
}

//添加 catch 方法
Promise.prototype.catch = function(onRejected){
    return this.then(undefined, onRejected);
}
```

### resolve方法封装

```javascript
//添加 resolve 方法
Promise.resolve = function(value){
    //返回promise对象
    return new Promise((resolve, reject) => {
        if(value instanceof Promise){
            value.then(v=>{
                resolve(v);
            }, r=>{
                reject(r);
            })
        }else{
            //状态设置为成功
            resolve(value);
        }
    });
}
```

### reject方法封装

```javascript
//添加 reject 方法
Promise.reject = function(reason){
    return new Promise((resolve, reject)=>{
        reject(reason);
    });
}
```

### all方法封装

```javascript
//添加 all 方法
Promise.all = function(promises){
    //返回结果为promise对象
    return new Promise((resolve, reject) => {
        //声明变量
        let count = 0;
        let arr = [];
        //遍历
        for(let i=0;i<promises.length;i++){
            //
            promises[i].then(v => {
                //得知对象的状态是成功
                //每个promise对象 都成功
                count++;
                //将当前promise对象成功的结果 存入到数组中
                arr[i] = v;
                //判断
                if(count === promises.length){
                    //修改状态
                    resolve(arr);
                }
            }, r => {
                reject(r);
            });
        }
    });
}
```

### race方法封装

```javascript
//添加 race 方法
Promise.race = function(promises){
    return new Promise((resolve, reject) => {
        for(let i=0;i<promises.length;i++){
            promises[i].then(v => {
                //修改返回对象的状态为 『成功』
                resolve(v);
            },r=>{
                //修改返回对象的状态为 『失败』
                reject(r);
            })
        }
    });
}
```

### then方法回调的异步执行

```javascript
let p1 = new Promise((resolve, reject) => {
    reject('OK');
    console.log(111);
});

p1.then(value => {
    console.log(222);
}, reason => {
    console.log(444);
});

console.log(333);
//正常情况输出 顺序
//111
//333
//444
```

`then`指定的回调函数是异步执行的，要让其异步执行需要加上定时器

```javascript
function Promise(executor){
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    this.callbacks = [];
    const self = this;// self _this that
    function resolve(data){
        if(self.PromiseState !== 'pending') return;
        self.PromiseState = 'fulfilled';
        self.PromiseResult = data;
				// 1
				//执行成功的回调
        setTimeout(() => {
            self.callbacks.forEach(item => {
                item.onResolved(data);
            });
        });
    }
    function reject(data){
        if(self.PromiseState !== 'pending') return;
        self.PromiseState = 'rejected';// 
        self.PromiseResult = data;
        // 2
				//执行失败的回调
        setTimeout(() => {
            self.callbacks.forEach(item => {
                item.onRejected(data);
            });
        });
    }
    try{       
        executor(resolve, reject);
    }catch(e){
        reject(e);
    }
}

Promise.prototype.then = function(onResolved, onRejected){
    const self = this;
    if(typeof onRejected !== 'function'){
        onRejected = reason => {
            throw reason;
        }
    }
    if(typeof onResolved !== 'function'){
        onResolved = value => value;
    }
    return new Promise((resolve, reject) => {
        function callback(type){
            try{
                let result = type(self.PromiseResult);                
                if(result instanceof Promise){
                    result.then(v => {
                        resolve(v);
                    }, r=>{
                        reject(r);
                    })
                }else{             
                    resolve(result);
                }
            }catch(e){
                reject(e);
            }
        }
        if(this.PromiseState === 'fulfilled'){
						// 3
            setTimeout(() => {
                callback(onResolved);
            });
        }
        if(this.PromiseState === 'rejected'){
            // 4
						setTimeout(() => {
                callback(onRejected);
            });
        }
        if(this.PromiseState === 'pending'){
            this.callbacks.push({
                onResolved: function(){
                    callback(onResolved);
                },
                onRejected: function(){
                    callback(onRejected);
                }
            });
        }
    })
}
```

### class版本实现

```javascript
class Promise{
    //构造方法
    constructor(executor){
        //添加属性
        this.PromiseState = 'pending';
        this.PromiseResult = null;
        //声明属性
        this.callbacks = [];
        //保存实例对象的 this 的值
        const self = this;// self _this that
        //resolve 函数
        function resolve(data){
            //判断状态
            if(self.PromiseState !== 'pending') return;
            //1. 修改对象的状态 (promiseState)
            self.PromiseState = 'fulfilled';// resolved
            //2. 设置对象结果值 (promiseResult)
            self.PromiseResult = data;
            //调用成功的回调函数
            setTimeout(() => {
                self.callbacks.forEach(item => {
                    item.onResolved(data);
                });
            });
        }
        //reject 函数
        function reject(data){
            //判断状态
            if(self.PromiseState !== 'pending') return;
            //1. 修改对象的状态 (promiseState)
            self.PromiseState = 'rejected';// 
            //2. 设置对象结果值 (promiseResult)
            self.PromiseResult = data;
            //执行失败的回调
            setTimeout(() => {
                self.callbacks.forEach(item => {
                    item.onRejected(data);
                });
            });
        }
        try{
            //同步调用『执行器函数』
            executor(resolve, reject);
        }catch(e){
            //修改 promise 对象状态为『失败』
            reject(e);
        }
    }

    //then 方法封装
    then(onResolved,onRejected){
        const self = this;
        //判断回调函数参数
        if(typeof onRejected !== 'function'){
            onRejected = reason => {
                throw reason;
            }
        }
        if(typeof onResolved !== 'function'){
            onResolved = value => value;
            //value => { return value};
        }
        return new Promise((resolve, reject) => {
            //封装函数
            function callback(type){
                try{
                    //获取回调函数的执行结果
                    let result = type(self.PromiseResult);
                    //判断
                    if(result instanceof Promise){
                        //如果是 Promise 类型的对象
                        result.then(v => {
                            resolve(v);
                        }, r=>{
                            reject(r);
                        })
                    }else{
                        //结果的对象状态为『成功』
                        resolve(result);
                    }
                }catch(e){
                    reject(e);
                }
            }
            //调用回调函数  PromiseState
            if(this.PromiseState === 'fulfilled'){
                setTimeout(() => {
                    callback(onResolved);
                });
            }
            if(this.PromiseState === 'rejected'){
                setTimeout(() => {
                    callback(onRejected);
                });
            }
            //判断 pending 状态
            if(this.PromiseState === 'pending'){
                //保存回调函数
                this.callbacks.push({
                    onResolved: function(){
                        callback(onResolved);
                    },
                    onRejected: function(){
                        callback(onRejected);
                    }
                });
            }
        })
    }

    //catch 方法
    catch(onRejected){
        return this.then(undefined, onRejected);
    }

    //添加 resolve 方法
    static resolve(value){
        //返回promise对象
        return new Promise((resolve, reject) => {
            if(value instanceof Promise){
                value.then(v=>{
                    resolve(v);
                }, r=>{
                    reject(r);
                })
            }else{
                //状态设置为成功
                resolve(value);
            }
        });
    }

    //添加 reject 方法
    static reject(reason){
        return new Promise((resolve, reject)=>{
            reject(reason);
        });
    }

    //添加 all 方法
    static all(promises){
        //返回结果为promise对象
        return new Promise((resolve, reject) => {
            //声明变量
            let count = 0;
            let arr = [];
            //遍历
            for(let i=0;i<promises.length;i++){
                //
                promises[i].then(v => {
                    //得知对象的状态是成功
                    //每个promise对象 都成功
                    count++;
                    //将当前promise对象成功的结果 存入到数组中
                    arr[i] = v;
                    //判断
                    if(count === promises.length){
                        //修改状态
                        resolve(arr);
                    }
                }, r => {
                    reject(r);
                });
            }
        });
    }

    //添加 race 方法
    static race (promises){
        return new Promise((resolve, reject) => {
            for(let i=0;i<promises.length;i++){
                promises[i].then(v => {
                    //修改返回对象的状态为 『成功』
                    resolve(v);
                },r=>{
                    //修改返回对象的状态为 『失败』
                    reject(r);
                })
            }
        });
    }
}
```

