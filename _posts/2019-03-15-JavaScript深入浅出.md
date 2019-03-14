---
layout: post
title: JavaScript深入浅出
date: 2019-03-15
categories: JavaScript
tags: JavaScript
---
# JavaScript深入浅出
## JS六种数据类型

* Object
	* Function
	* Array
	* Date
	* ...
* 原始类型
	* number
	* string
	* boolean
	* null
	* undefined

## 隐式转换
* `+`和`-`
	* `+`：一个string类型加上一个number类型，`+`看作是字符串的拼接
	* `-`:一个string类型减去一个number类型，`-`看作是数字运算

## 包装类型
* `var str = "string";`   
这是变量的类型就是基本类型`string`
* `var strObj = new String("string");`   
这个变量的类型是`string`类型对应的**包装类型**

Js 中基本类型`number`,`string`,`boolean`都有对应的包装类型。当把上述一个基本类型的变量尝试以对象的形式操作时，JS会创建一个临时的包装类型，它的值和这个基本类型是一样的，但是当操作结束后，这个临时变量就被销毁了。

## 类型检测
* 1.`typeof`   
	返回一个字符串，注意
	`typeof null === "object"`
	而且在判断数组的时候就不好用这个，因为其返回的是对象
* 2.`instanceof `  
	用法`obj instanceof Object`，obj指对象，而Object指函数对象（函数构造器），判断左操作数对象的原型链上是不是有右操作数的prototype。注意不同的window或iframe之间的对象类型检测不能用它
* 3.`Object.prototype.toString.apply()`  
	例如： `Object.prototype.toString.apply([]) === "[object Array]"`



## Javascript OOP 面向对象程序设计
* `prototype`属性和原型初步理解
	* 每一个函数对象都有会有一个`prototype`属性，这个属性是一个对象称为构造子，给这个对象也可以增加属性，而且这个对象也有一个`constructor`属性，这个属性也是对象，正是这个构造函数本身；
	* 而原型是对象的原型，一般用`_proto_`表示，通过这个构造函数new出来的对象实例的原型`_proto_`就是这个函数的构造子（也就是这个函数的prototype属性对象）
	
	```
	funtion Foo() {
		this.y = 2;
	};
	typeof Foo.prototype;//object
	Foo.prototype.x = 1;
	var obj = new Foo();
	obj.x;//1
	obj.y;//2
	obj._proto_ === Foo.prototype;//true
	Foo.prototype {
	    constructor: FOO,
	    _proto_:Object.prototype,
	    x = 1;
	}
	```
   ![](JavaScript深入浅出-2.png)

* 基于原型的继承
	* 使用`Object.create`,这是比较提倡的方法

	```
	function Person() {}
	function Strudent() {}
	
	Student.prototype = Object.create(Person.prototype);
	Student.prototype.constructor = Student;//保持一致性
	```
	`Object.create(Person.prototype)`函数创建一个空对象，这个空对象的原型就是`Person.prototype`，即Person的构造子，让Student的构造子等于它，就生成了一条原型链    
	![](JavaScript深入浅出-1.jpeg)
	
	* 使用`new`的方法，虽然这样也可以实现继承，但是如果函数本身内部有些属性，在实例化的时候不好传入参数，比如下图中如果`Person`函数有名字和年龄属性，那么`new`的时候应该传入什么值呢；使用1处的方法是不对的，这样子没办法做到继承，给`Student`加自己的方法也会影响到`Person`;3是理想的继承方式，但是在ES5之后才支持，可以用右侧的方法进行兼容。(返回一个空对象，并且原型指向参数)    
	![](JavaScript深入浅出-8.png)
	* 注意：大多数 **函数对象** 的 **prototype属性对象** 的 **原型** 都是 **Object对象** 的 **prototype对象** 。也正因为如此，大多数对象都有`toString`， `valueof`等继承自`Object.prototype`的方法，但也有一些特例：
		* 通过`Object.create(null)`创建出来的对象，就没有`_proto_`，因为它创建了一个空对象而且这个空对象的原型指向`null`，因此它也不会有`toString`等方法
		
		```
		var obj2 = Object.create(null);
		obj2._proto_;//undefined
		obj2.toString;//undefined
		```
		![](JavaScript深入浅出-3.png)
		
      * 通过`bind`方法返回的函数就没有`prototype`属性
  
		```
		funtion abc() {}
		abc.prototype; //abc {}
		var binded = abc.bind(null);
		typeof binded; //function
		binded.prototype; //undefined
		```
		![](JavaScript深入浅出-4.png)

* prototype属性
	* 改变prototype
		* 给函数对象的prototype对象添加新的属性会影响到之前已经（new）创建实例化出的函数实例，这个函数实例会继承到这个属性，但是如果改写了这个函数的prototype，之前已经创建实例化的对象并不会受到影响，而之后再创建的对象会以这个新的prototype对象为原型

		```
		function Student() {};
		var bosn = new Student();
		Student.prototype.x = 101;
		bosn.prototype;//101
		
		Student.prototype = {y:2};
		bosn.y;//undefined
		bosn.x;//101;
		
		var mary = new Student();
		marry.y;//2
		maty.x;//undefined
		```
		![](JavaScript深入浅出-5.png)
		
	* 内置构造器的prototype属性
		* 修改构造器的prototype会有边界效应，就是在for遍历的时候会变量出修改添加的属性，要想避免这个效应，可以用define property设置相应的属性。
		* 例如：假如大多数对象上希望有某个属性x，设置`Object.property.x=1`就会有边界效应（`for in`的时候会把这个x遍历出来）。可以通过`defineproperty`设置相应的属性。
		![](JavaScript深入浅出-6.png)
		
	* 检测某个对象是否有某个属性：
		* `属性 in 对象`:这会在整个原型链上查找这个属性
		* `obj.hasOwnProperty('z')`这只会在这个对象及它的原型上查找这个属性
		![](JavaScript深入浅出-7.png)
 
 * 模拟重载
	![](JavaScript深入浅出-9.png)
	

## 函数和作用域
Js 中的函数也是对象

* 创建函数
	* 函数申明 
		![](JavaScript深入浅出-10.png)
	* 函数表达式
		![](JavaScript深入浅出-11.png)
	* 区别：函数申明可以前置，被提升，而函数表达式不会