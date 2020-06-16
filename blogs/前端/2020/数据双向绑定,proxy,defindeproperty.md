---
title: 数据双向绑定,proxy,defindeproperty
date: 2020-06-16
tags:
 - javascript
 - vue
categories:
 - 前端
---

## defindeproperty与proxy对比

1. defindeproperty 检测不到对象属性的添加和删除：当你在对象上新加了一个属性newProperty，当前新加的这个属性并没有加入vue检测数据更新的机制(因为是在初始化之后添加的)。vue.$set是能让vue知道你添加了属性, 它会给你做处理，$set内部也是通过调用Object.defineProperty()去处理的
2. defindeproperty 无法监控到数组下标的变化，导致直接通过数组的下标给数组设置值，不能实时响应。
3. 使用defindeproperty时， 当data中数据比较多且层级很深的时候，会有性能问题，因为要遍历data中所有的数据并给其设置成响应式的。
4. Object.defineProperty() 是用来拦截对象的属性的。Proxy是拦截对象，外界对该对象的访问，都必须先通过这层拦截。无论访问对象的什么属性，之前定义的还是新增的，它都会走到拦截中，他还可以拦截数组。
5. 参考：[简单通俗的理解Vue3.0中的Proxy](https://juejin.im/post/5e69ee2be51d4527196d6a24)，[初探 Vue3.0 中的一大亮点——Proxy!](https://www.jianshu.com/p/2a8ec76e0090)，[再聊一下用Proxy实现双向数据绑定](https://blog.csdn.net/weixin_33711641/article/details/88802384)，

## dom部分

```html
<div id="app">
    <h3 id="paragraph"></h3>
    <input type="text" id="input" />
</div>
```

## defindeproperty

```javascript
//获取段落的节点
const paragraph = document.getElementById('paragraph');
//获取输入框节点
const input = document.getElementById('input');
//需要代理的数据对象
const data = {
    // text: 'hello world'
}
Object.defineProperty(data, 'text', {
    //监控 data 中的 text 属性变化
    set: function (value) {
        paragraph.innerHTML = value;
        input.value = value;
    }
}
);
//添加input监听事件
input.addEventListener('input', function (e) {
    data.text = e.target.value;
}, false)
// console.log(data.text)
//初始化值
data.text = 'hello world';

```

## proxy

```javascript
//获取段落的节点
const paragraph = document.getElementById('paragraph');
//获取输入框节点
const input = document.getElementById('input');
//需要代理的数据对象
const data = {
    text: 'hello world'
}
const handler = {
    //监控 data 中的 text 属性变化
    set: function (target, prop, value) {
        if (prop === 'text') {
            //更新值
            target[prop] = value;
            //更新视图
            paragraph.innerHTML = value;
            input.value = value;
            return true;
        } else {
            return false;
        }
    }
}
//添加input监听事件
input.addEventListener('input', function (e) {
    myText.text = e.target.value;   //更新 myText 的值
}, false)
//构造 proxy 对象
const myText = new Proxy(data, handler);
//初始化值
myText.text = data.text;  
```
