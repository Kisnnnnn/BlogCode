---
title: Promise构造函数和then方法的执行顺序
date: 2019-08-14 21:00:00 +0800
categories: 前端
tag: 笔记
---
## Promise的基本用法
```js
  let promise = new Promise((resolve, reject) => {
        console.log(1)
        // 如果执行失败，则调用reject()
        // reject('fail')
        
        console.log(2)
        
        // 如果执行成功，则调用resolve()
        resolve('success')
      })

      // 如果执行成功，则继续调用then方法
      promise.then(res => {
        // console.log(res)
        console.log(3)
      })

      // 如果执行失败，则用catach捕获失败
      promise.catch(err => {
        console.log(err)
        console.log(4)
      })
```

在`Promise`的内部，执行顺序是同步的。其次等内部执行完成后，调用再选择调用`then`or`catch`方法。

所以这次输出的结果：
> 1
2
3

## then的执行顺序
```js
   let promise = new Promise((resolve, reject) => {
        console.log(1)
        // 如果执行失败，则调用reject()
        // reject('fail')
        console.log(2)
        // 如果执行成功，则调用resolve()
        resolve('success')
      })

      // 如果执行成功，则继续调用then方法
      promise.then(res => {
        console.log(res)
        console.log(3)
      })

      // promise内部是同步的，但是then方法是异步的
      console.log(5)
```

`then`方法执行是异步的，不用等到`then`的方法结果,会直接执行`console.log(5)`

> 1
2
5
3

### 我们可以根据上面的代码，丰富业务实际情况
```js
      let promise = new Promise((resolve, reject) => {
        console.log(`开始执行`)
        
        // 如果执行成功，则调用resolve()
        resolve('success')
        
        console.log(`执行中ing`)
      }).then(res => {
        console.log(res)
        console.log('执行成功，继续执行下一步')
        return '第二then方法，开始'
      })

      // 如果执行成功，则继续调用then方法
      promise.then(res => {
        console.log(res)
      })

      // promise内部是同步的，但是then方法是异步的
      console.log(`我会在then方法前，先执行`)
      
      // 我们可以利用timeout方法在then方法执行完成后，进行执行
      setTimeout(()=>console.log(`then方法执行完成后，开始执行`))
```

#### 执行结果：

> 1.开始执行
2.执行中ing
3.我会在then方法前，先执行
4.执行成功，继续执行下一步
5.第二then方法，开始
6.then方法执行完成后，开始执行

### 总结

其实比较好理解的方式是：每次当`promise`执行完成后，`then`是异步的，所以当执行`then`后，不会等它的结果，会直接执行下面的代码，但是`setTimeout`定时器本身会将执行内容调到`任务队列`尾部，所以会等`then`方法执行完成后才会调用定时器。





