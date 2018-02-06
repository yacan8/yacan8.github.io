---
title: JavaScript常用的设计模式
date: 2018-02-06 16:19:20
tags:
- 设计模式
- JavaScript
---


## 单例模式
概念：
保证一个类仅有一个实例，并提供一个访问它的全局访问点。实现的方法为先判断实例存在与否，如果存在则直接返回，如果不存在就创建了再返回，这就确保了一个类只有一个实例对象。

意义：
* 对象只有一个实例，节约系统资源，提供性能。
* 提供对唯一实例的受控访问。
* 类可以灵活的改变实例化过程。

应用场景：redux下的store、浏览器写某个特定的浮框...

示例：
```js
const Singleton = name => {
  this.name = name;
};
Singleton.prototype.getName = () => {
  console.log(this.name)
};

Singleton.getInstance = (() => {
  const instance = null;
  return name => {
    if (!instance ) {
      instance = new Singleton( name );
    }
    return instance;
  }
})();

const a = Singleton.getInstance('sven1');
const b = Singleton.getInstance('sven2');
console.log(a === b); // true
```

## 代理模式
概念：
为对象提供一个代用品或者占位符，以便控制对它的访问。

意义：
* 单一职责，只有代理对象才能引起该对象的变化。
* 用代理来过滤掉一些不合规的的访问，减少该对象的访问，在某种意义上可以提高性能。

应用场景：图片预加载...

示例：
```js
var myImage = (function() {
  var imgNode = document.createElement('img');
  document.body.appendChild(imgNode);
  return {
    setSrc: function(src) {
      imgNode.src = src;
    }
  }
})();
var proxyImage = (function(){
  var img = new Image;
  img.onload = function() {
    myImage.setSrc(this.src );
  }
  return {
    setSrc: function(src) {
      myImage.setSrc('loading.gif');
      img.src = src;
    }
  }
})();

proxyImage.setSrc('http://imgcache.qq.com/music/photo/k/000GGDys0yA0Nk.jpg');
```
## 装饰者模式
概念：在不改变对象自身的基础上，在程序运行期间给对象动态地添加方法。

意义：
* 原有方法维持不变，在原有方法上再挂载其他方法来满足现有需求；函数的解耦，将函数拆分成多个可复用的函数，再将拆分出来的函数挂载到某个函数上，实现相同的效果但增强了复用性。
* 拓展对象功能。

应用场景：动态表单验证...

示例：
```js
function Sale(price) {
  this.price = price;
  this.decorateList = [];
}
Sale.decorators = {};
Sale.decorators.fedtax = {
  getPrice: function(price) {
    var price = this.uber.getPrice();
    return price * 0.8; //对price进行处理
  },
}
Sale.decorators.quebec = {
  getPrice: function(price) {
    var price = this.uber.getPrice();
    return price * 0.7; //对price进行处理
  },
}
Sale.decorators.money = {
  getPrice: function(price) {
    var price = this.uber.getPrice();
    return "$" + price * 0.9; //对price进行处理
  },
}
Sale.prototype.decorate = function(decorator) {
  this.decorateList.push(decorator);
};
Sale.prototype.getPrice = function() {
  var price = this.price;
  this.decorateList.forEach(function(name) {
    price = Sale.decorators[name].getPrice(price);
  });
  return price;
};
var sale = new Sale(100);
sale = sale.decorate("fedtax"); //联邦税
sale = sale.decorate("quebec"); //魁北克省税
sale = sale.decorate("money"); //转为美元格式
console.log(sale.getPrice()); //$50.4
```

## 策略模式
概念：定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。

意义：
* 利用组合，委托等技术和思想，有效的避免很多if条件语句。
* 提供了开放-封闭原则，使代码更容易理解和扩展。
* 代码复用。

应用场景：表单校验

示例：
```js
var strategies = {
  isNonEmpty: function( value, errorMsg ){
    if ( value === '' ) {
      return errorMsg;
    }
  },
  minLength: function( value, length, errorMsg ){
    if ( value.length < length ) {
      return errorMsg;
    }
  },
  isMobile: function( value, errorMsg ){ // 手机号码格式
    if ( !/(^1[3|5|8][0-9]{9}$)/.test( value ) ){
      return errorMsg;
    }
  }
};

var Validator = function(){
  this.cache = []; // 保存校验规则
};
Validator.prototype.add = function(dom, rule, errorMsg) {
  var ary = rule.split( ':' ); // 把 strategy 和参数分开
  this.cache.push(function(){ // 把校验的步骤用空函数包装起来，并且放入 cache
    var strategy = ary.shift(); // 用户挑选的 strategy
    ary.unshift( dom.value ); // 把 input 的 value 添加进参数列表
    ary.push( errorMsg ); // 把 errorMsg 添加进参数列表
    return strategies[strategy].apply(dom. ary);
  })
}
Validator.prototype.start = function(){
  for ( var i = 0, validatorFunc; validatorFunc = this.cache[ i++ ]; ){
    var msg = validatorFunc(); // 开始校验，并取得校验后的返回信息
    if ( msg ){ // 如果有确切的返回值，说明校验没有通过
      return msg;
    }
  }
};

var validataFunc = function(){
  var validator = new Validator(); // 创建一个 validator 对象
    /***************添加一些校验规则****************/
    validator.add( registerForm.userName, 'isNonEmpty', '用户名不能为空' );
    validator.add( registerForm.password, 'minLength:6', '密码长度不能少于 6 位' );
    validator.add( registerForm.phoneNumber, 'isMobile', '手机号码格式不正确' );
    var errorMsg = validator.start(); // 获得校验结果
    return errorMsg; // 返回校验结果
  }
  var registerForm = document.getElementById( 'registerForm' );
  registerForm.onsubmit = function(){
    var errorMsg = validataFunc(); // 如果 errorMsg 有确切的返回值，说明未通过校验 if ( errorMsg ){
    console.log( errorMsg );
    return false; // 阻止表单提交
  }
};
```
