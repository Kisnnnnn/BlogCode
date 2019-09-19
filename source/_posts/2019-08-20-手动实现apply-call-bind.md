---
title: 手动实现apply-call-bind
date: 2019-08-20 21:00:00 +0800
categories: 前端
tag: 笔记
---

### apply
```js
Function.prototype.myApply = function(context,args){
    // 如果是调用自己，则返回undefined
    if(this===Function.prototype) return false;
    
    context=context || window;

    var fn = Symbol(),
    result;
    
    context[fn]=this;
    
    if(Array.isarray(args)){
        result = context[fn](...args);
    }else{
        result = context[fn]();
    }
    
    delete context[fn];
    return result;
}
```

1.如果是调用自己，则返回undefined
2.判断`context`是否存在，不存在则调用window
3.新增一个`Symbol`属性，赋值给`context`
4.将当前函数赋值给这个属性
5.判断传参是否数组
6.如果是，则将其余的参数传入执行方法。如果否，则直接调用方法。
7.删除方法，返回。


### call
```js
Function.prototype.myCall= function(context=window,...args){
    if(this === Function.prototype) return undefined;
    
    context= context || window
    
    var fn = Symbol(),
    result;
    
    context[fn] = this;
    
    result = context[fn](...args)
    delete context[fn]
    return result;
}
```
1.如果是调用自己，则返回undefined
2.判断`context`是否存在，不存在则调用window
3.新增一个`Symbol`属性，赋值给`context`
4.将当前函数赋值给这个属性
5.将其余的参数传入执行方法
6.删除方法，返回。

### bind
```js
Function.prototype.myBind = function(context=window,...args){
    if(this === Function.prototype) return undefined;
    
    const _this = this ;
    
    return function F(...args2){
        if(this instanceof F){
            return new _this(...args,...args2)
        }
        _this.apply(context,args.concat(...args2))
    }
}
```

1.处理参数，返回一个闭包
2.判断是否为构造函数调用，如果是则调用`new`调用当前函数
3.如果不是，则用`apply`，将`context`和`args`参数传入


[参考](http://www.conardli.top/docs/JavaScript/%E6%89%8B%E5%8A%A8%E5%AE%9E%E7%8E%B0call%E3%80%81apply%E3%80%81bind.html#%E6%A8%A1%E6%8B%9F%E5%AE%9E%E7%8E%B0apply)
