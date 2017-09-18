### JavaScript面向对象的程序设计

##### 属性类型
 两种属性：  
 	- 数据属性  
 	- 访问器属性
 	
##### 数据属性
  	
 - [[Configurable] 	// 能否修改属性
 - [[Enumberable]] // 属性是否可以用for-in遍历
 - [[Writable]] // 能否修改[[value]]
 - [[Value]] // 数据值
 
##### 访问器属性
- [[Configurable]]
- [[Enumberable]]
- [[Get]] 读取属性时调用
- [[Set]] 写入属性时调用

##### 属性操作
Object.defineProperty // 修改属性的特性  
Object.getOwnPropertyDescriptor // 读取属性的特性

```
Object.defineProperty(obj,"year", {
	get: function() {
		return this._year;
	},
	set: function(newValue) {
		this._year = newValue;
	}
})
var descriptor = Object.getOwnPropertyDescriptor(obj,'_year');
// {value: 1,configurable: false ...}
```

### 创建对象

##### 工厂模式

```
function createPerson(name) {
    var o = new Object();
    o.name = name;
    o.sayName = function() {
        console.log(this.name);
    }
    return o;
}
var p1 = createPerson('zhuzhu');
var p2 = createPerson('xixi');
```
问题：没有解决对象识别的问题，typeof p1 // object


##### 构造函数模式

```
function Parent(name) {
    this.name = name;
    this.sayName = function() {
      console.log(this.name);
    }
}
var c1 = new Parent('zhuzhu');
var c2 = new Parent('xixi');
```
new 的过程：  
1. 创建新对象  
2. 将构造函数的作用域赋给新对象（this指向这个新对象）  
3. 执行构造函数中的代码  
4. 返回新对象  

问题：每次生成实例都会生成Function的实例，改进：方法设为全局函数，然后对象的属性指向它，但是这样会造成全局作用域有太多只给某对象使用的方法

##### 原型模式

```
function Parent(){}
Parent.prototype.name = 'nicholos';
Parent.prototype.sayName = function() {
      console.log(this.name);
}
var r1 = new Parent();
var r2 = new Parent();
```
问题：所有实例共享一份属性和方法

##### 组合使用构造函数模式和原型模式

```
function Person(name) {
    this.name = name;
}
Person.prototype = {
    constructor: Person,
    sayName: function() {
       console.log(this.name);
    }
}
	
var c1 = new Person('zhuzhu');
var c1 = new Person('xixi');
```
推荐使用👍
#####  动态原型方式

```
function Person(name) {
    this.name = name;
    if (typeof this.sayName != 'function') {
       Person.prototype.sayName = function() {
           console.log(this.name);
       }
    }
}
var c1 = new Person('xixi');
```
##### 寄生构造函数

```
function SpecialArray() {
    // 创建数组
    var values = new Array();
    // 添加值
    values.push.apply(values, arguments);
    // 添加方法
    values.toPipedString = function() {
    ¦   return this.join("|");
    }
    return values;
}
var colors = new SpecialArray("red", "blue", "green");
alert(colors.toPipedString());
```
除了用了new，和工厂模式一般无二，此法一般用于在不能对已有的构造函数进行修改的情况下，对原来的构造函数进行扩充。
##### 稳妥构造函数模式

```
function Person(name) {
    var o = new Object();
    // 在这里定义私有变量和函数
    o.sayName = function() {
       alert(name);
    }
    return o;
}
var p1 = Person('haha');
```
稳妥对象，没有公共属性，方法不使用this，上面的name就是私有属性，只有sayName能访问，适合在某些安全执行环境

### 继承
##### 原型对象
每个函数有一个prototype属性，指向它的原型对象 Prototype  

 	f.prototype -> f Prototype

原型对象里constructor 指向函数  

	f.prototype.constructor -> f
 
f的实例有[[prototype]]属性  

	[[prototype]] -> f Prototype

检测是否是这种关系， isPrototypeOf / Object.getPrototypeOf

	Person.prototype.isPrototypeOf(p1)
	// or
	Object.getPrototypeOf(p1) == Person.prototype

检测某个属性是否存在于实例上 `hasOwnProperty `   
检测某个属性是否可以通过实例访问（实例+原型） `in `   
获取对象上所有可枚举的实例属性 `Object.keys `   
获取所有实例属性 `Object.getOwnPropertyNames `   

##### 原型链
构造函数，原型，实例的关系  
函数的默认原型对象是Object的实例

```
 Object
   ||
   \/
SuperType (原型对象是Object的实例)
   ||
   \/
 SubType (原型对象是SuperType的实例)  
``` 
- 确定原型和实例的关系 
	- instanceof
	- isPrototypeOf
- 原型链的问题
 - 因为继承时，子类的原型对象变成了父类的实例，所以父类的实例属性就变成了子类的原型属性，为所有子类实例共享。
 - 创建子类实例时，无法向父类构造函数传参

##### 继承方法之 借用构造函数

```
function SuperType(name) {
    this.name = name;
    this.colors = ["res", "green"];
}
function SubType() {
    SuperType.call(this, "nicholos");
    this.age = 1;
}
var instance = new SubType();
```
可以给父类的构造函数穿餐，但是函数复用无从谈起

##### 继承方法之 组合继承

```
function SuperType(name) {
    this.name = name;
    this.colors = ["res", "green"];
}
SuperType.prototype.sayName = function() {
    alert(this.name);
}
function SubType(name, age) {
    SuperType.call(this, name);
    this.age = age;
}
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function() {
    alert(this.age);
}
var instance = new SubType("nicholos", 13);
```
问题：调用了两次父类构造函数
##### 继承方法之 原型式继承

```
// 原型式继承
function object(o) {
    function F(){}
    F.prototype = o;
    return new F();
}
```
适用于与另一个对象类似的情况下  
问题 所有实例共享o，实例的操作会影响o的值  
此方法与Object.create()方法行为相同  

##### 继承方法之 寄生式继承
 
```
function createAnother(orginal) {
	var clone = object(original);  // 创建新对象
	// 增强对象
	clone.sayHi = function() {
		alert('hi';)
	}
	return clone;
}
```
问题：与构造函数模式类似，不能做到函数复用

##### 继承方法之 寄生组合式继承 **
解决组合继承两次调用构造函数的问题

```
function inheritPrototype(SubType, SuperType) {
    var prototype = Object(SuperType.prototype); // 创建对象
    prototype.constructor = SubType;
    SubType.prototype = prototype;
}

function SuperType(name) {
    this.name = name;
    this.colors = ["res", "green"];
}
SuperType.prototype.sayName = function() {
    alert(this.name);
}
function SubType(name, age) {
    SuperType.call(this, name);
    this.age = age;
}
inheritPrototype(SubType, SuperType);
SubType.prototype.sayAge = function() {
    alert(this.age);
}
var instance = new SubType("nicholos", 13);
```

-----------------------


### 函数
##### 函数声明

```
// 函数声明
var function functionName(arg0,arg1,arg2) {
	// 函数体
}
```
1. 函数声明提升，在执行前会先对函数声明，可以把调用的语句放在声明前


##### 函数表达式

```
// 匿名函数（anonymous function）/ 拉姆达函数
var functionName = function(a1,a2) {
	// 函数体
}
```
没有变量提升

#### 递归
	- arguments.callee // 严格模式下不能使用
	- 命名函数表达式
	 
	```
	var fectorial = (function f(num) {
		if (num <= 1) {
			return 1;
		} else {
			return num * f(num - 1);
		}
	});
	```
#### 闭包 
函数里面定义了其他函数时，就创建了闭包 
 作用域的问题
	在一个函数内部定义的函数会将包含函数的活动对象添加到它的作用域中

关于闭包的几个例子：
http://stackoverflow.com/questions/111102/how-do-javascript-closures-work	

```
Example 1:
var gLogNumber, gIncreaseNumber, gSetNumber;
function setupSomeGlobals() {
  // Local variable that ends up within closure
  var num = 42;
  // Store some references to functions as global variables
  gLogNumber = function() { console.log(num); }
  gIncreaseNumber = function() { num++; }
  gSetNumber = function(x) { num = x; }
}

setupSomeGlobals();
gIncreaseNumber();
gLogNumber(); // 43
gSetNumber(5);
gLogNumber(); // 5

var oldLog = gLogNumber;

setupSomeGlobals();
gLogNumber(); // 42

oldLog() // 5
```

```
Example 2:
 function buildList(list) {
    var result = [];
    for (var i = 0; i < list.length; i++) {
        var item = 'item' + i;
        result.push( function() {console.log(item + ' ' + list[i])} );
    }
    return result;
}

function testList() {
    var fnlist = buildList([1,2,3]);
    // Using j only to help prevent confusion -- could use i.
    for (var j = 0; j < fnlist.length; j++) {
        fnlist[j]();
    }
}

 testList() //logs "item2 undefined" 3 times
```
 - 每个执行环境有一个变量对象
 - 作用域链是一个指向变量对象的指针列表
 
 过程  
 - 在创建局部函数时，会创建它的作用域链，保存在[[Scope]]中
 - 调用局部函数时，创建执行环境，然后复制[[Scope]]属性中的对象构建执行环境的作用域链，活动对象被创建并推入执行环境作用域的前端。
 - 一般，函数执行完后，局部活动对象被销毁，内存中仅保留全局作用域，但是闭包不是这样

原理如下：
1. 在后台执行环境中，闭包的作用域链包含着它自己的作用域，包含函数的作用域和全局作用域
2. 通常，函数的作用域及其所有变量都会在函数执行结束后被销毁。
3. 但是，当函数返回一个闭包时，这个函数的作用域将会一直在内存中保存到闭包不存在为止。

因为闭包会携带包含它的函数的作用域，因此占用较多的内存。

#### 关于this对象
this对象是在运行时基于函数的执行环境绑定的
匿名函数的执行环境具有全局性

```
var name = "the window";
var obj = {
	name: 'haha',
	getName: function() {
		return function() {
			return this.name;
		}
	}
}
obj.getName()(); // window
```
#### 内存泄露
避免循环引用，和必要的置空

```
function assignHandler() {
	var element = document.getElementById("someElement");
	var id = element.id;
	element.onclick = function() {
		alert(id);
	}
	element = null;
}
```

#### 模仿块级作用域
JavaScript没有块级作用域  
模仿块级作用域／私有作用域   
* 匿名函数

```
(f(){
	// 块级作用域
})();
```
将函数声明包含在一对圆括号中，表示它实际上是一个函数表达式  
函数声明后不能直接加括号

可以减少闭包占用的内存，因为没有指向匿名函数的引用，只要函数执行完毕，就可以立即销毁其作用域链了

#### 私有变量 
 `私有变量` 包括函数的参数，局部变量和内部定义的其他函数。  
`特权方法` 有权访问私有变量和私有函数的公有方法称为特权方法。

```
function Person(name) {
   	// 特权方法， name是私有变量
	this.getName = function() {
		return name;
	},
	this.setName = function(val) {
		name = val;
	}
}
```
##### 静态私有变量
```
(function(){
   // 私有变量和函数
	var privateVariable = 10;
	function privateFunction() {
		return false;
	}
	MyObject = function() {};
	MyObject.prototype.publicMethod = function() {
		privateVariable++;
		return privateFunction();
	};
	MyObject.prototype.getVal = function() {
		return privateVariable;
	};	
})()；
var o1 = new MyObject();
var o2 = new MyObject();
o1.publicMethod();
o2.getVal(); //11
```
私有变量和函数是由实例共享的

##### 模块模式
`单例`： 指的就是只有一个实例的对象。  
`模块模式`： 为单例创建私有变量和特权方法。  
创建单例可以用对象字面量的方式。

```
var singleton = function(){
   // 私有变量和函数
	var privateVariable = 10;
	function privateFunction() {
		return false;
	}
	return {
		publicProperty: true,
		publicMethod: function(){
			privateVariable++;
			return privateFunction();
		}
	}
})()；
```

##### 增强的模块模式
适合单例必须是某种类型的实例

```
var singleton = function(){
   // 私有变量和函数
	var privateVariable = 10;
	function privateFunction() {
		return false;
	}
	var object = new CustomType();
	object.publicProperty = true;
	object.publicMethod = function(){
			privateVariable++;
			return privateFunction();
		}
	}
	return object;
})()；
```



