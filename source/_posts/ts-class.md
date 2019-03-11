---
title: 通过类创建对象，遍历对象的属性
date: 2019-03-11 10:51:58
tags:
- TypeScript
---
众所周知，通常在遍历JS中的对象的时候，一般会使用`for...in`或者`Object.keys()`方法，同时要注意属性的`enumerable`设置。  
  
在ES5中，我们定义一个类的写法如下：  
```js
function Person(name) {
    this.name = name;
    Person.prototype.say = function () {
        console.log(name);
    };
}
var p = new Person('Shinji');
```
使用`for...in`遍历对象`p`可以看到输出了`name`和`say`，因为`for...in`会遍历所有自己包括原型链上的可枚举属性，所以不仅输出了`name`，也输出了`say`。  
  
在ES6之后，定义一个类可以直接使用`class`:
```js
class Person {
    constructor(name) {
        this.name = name;
    }
    say() {
        console.log(this.name);
    }
}
const p = new Person('Shinji');
```
同样使用`for...in`遍历对象`p`，只得到了`name`。  
使用`Object.getOwnPropertyDescriptors(Object.getPrototypeOf(p))`可以看到`constructor`和`say`的`enumerable`属性都是false，所以使用`for...in`遍历不出来。使用`Object.getOwnPropertyNames(Object.getPrototypeOf(p))`可以得到`["constructor", "say"]`，可以用来获取对象`p`拥有的方法，如果有继承，则需要再嵌套一层`getPrototypeOf`。  

在TypeScript中，定义一个类和es6差不多：
```ts
class Person {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    say() {
        console.log(this.name);
    }
}
let p = new Person('Shinji');
```
但在使用`for...in`遍历的时候，发现和es5的输出差不多，其实是因为编译选项中将`target`设置为了`ES5`，编译后的代码和上面es5的代码差不多，所以`say`的`enumerable`为true，如果设置`target`为`ESNEXT`，则就和es6的表现差不多了。  
总之，TypeScript和ES6的`class`并不一样，毕竟TypeScript还是需要编译成JavaScript的。