### JavaScript简介
#### JavaScript组成

- 核心语言（ECMAScript）
- 文档对象模型（DOM）提供对网页内容操作的接口
- 浏览器对象模型（BOM）提供与浏览器交互的接口

##### 网页加载流程  

1.浏览器一边下载HTML网页，一边开始解析  
2.解析过程，发现script标签，暂停解析，网页渲染的控制权转接JavaScript引擎  
4.如果script标签引用了外部脚本，就下载该脚本，否则直接执行  
5.执行完毕，控制权交还给渲染引擎，恢复往下解析HTML网页

脚本加载

- async 不会阻塞网页，边下载边往下解析，不保证顺序，谁先下完先执行，load事件前执行，FF， Safari和chrome支持
- defer 边下边解析，等到文档完全被解析和显示之后再执行，保证顺序，在DOMContentLoaded事件触发前执行，只适用于外部脚本（defer="defer"）

    其他：

* CSS文件的加载会阻塞JS文件的执行

    浏览器5个常驻线程：

* GUI渲染线程
* JavaScript引擎线程
* 定时器触发线程
* 事件触发线程
* http异步请求线程

##### 文档模式
- 混杂模式 文档没有类型声明，IE行为与IE5的行为相同
- 标准模式 严格的文档类型声明
- 准标准模式  使用过渡性(transitional)或框架集型(frameset)的文档类型触发

    不支持脚本或脚本禁用时会呈现<noscript>元素

##### JavaScript变量类型

###### 语法
- 变量区分大小写，标识符只能以字母,_, $开头
- 严格模式（"use strict"）下， 不确定的行为，不安全的操作会作出处理

###### 数据类型：

* undefined
* null
* Number
* String
* Boolean 
* Symbol (第六版新增)
* object

    Number

- 浮点数
- Number.MIN_VALUE/Number.MAX_VALUE
- parseInt()
- parseFloat()
- NaN & Infinity & -Infinity
- isNaN() & isFinite()
- Number() null -> 0, undefined -> NaN，"" -> 0，如果是对象，调用valueOf方法，如果转换的结果是NaN，调用对象的toString方法
- parseInt("af", 16), "" -> NaN
- parseFloat()， 16进制的都是0
- 八进制0，十六进制0x


String

- Unicode字符序列，每一个编码单元由一个16位二进制数表示。每一个Unicode字符由一个或两个编码单元来表示。
- charAt()
- replace()
- toUpperCase()
- toString() null和undefined没有此方法
- String() 转为字符串

    boolean

- "",null,undefined,NaN,0 -> false
- [],{} -> true
- 转为boolean, Boolean(msg)

    undefined

- var a, a == undefined true
- 未声明的变量只能进行一项操作typeof 
- undefined的值派生自null, null == undefined


object  

- new Object() 或 对象字面量
- 通过对象字面量定义对象时，实际上不会调用Object构造函数
-  .和[]都可以访问对象属性  
    []支持变量访问属性  
    当属性名包含会导致语法错误的字符，或者属性名使用的是关键字或保留字时，可以使用[]  
-  对象字面量作为函数参数，一般对那些必需值使用命名参数，而使用对象字面量来封装多个可选参数

    变量声明：var, let, const

###### 操作符
位操作符：~(not), &(and), |(or), ^(xor), <<, >>(有符号), >>>(无符号)

###### 相等操作符 == & ===  
== 转换后再比较  
=== 不转换直接比较  
== 比较规则  
跟数值比较有：  

- boolean -> number  
- string -> number  

跟对象比较有：

- 如果一个是对象，另一个不是，就调用对象的valueOf方法
- 两个都是对象，比较是不是指向同一个对象

其他：

- undefined == null, 不转换，undefined值派生自null值，因此有null == undefined
- NaN的相等操作符返回NaN，不等返回true 


###### label语句
label: statement
配合break和continue使用

###### Object属性和方法 

- constructor
- hasOwnProperty(propertyName): 检查给定的属性在当前对象实例中是否存在
- isPrototypeOf(object):检查传入的对象是否是另一个对象的原型
- propertyIsEnumberable(propertyName):检查给定的属性是否能够使用for-in 遍历
- toLocaleString: 返回对象的字符串表示
- toString:  返回对象的字符串表示
- valueOf: 返回对象的字符串、数值或布尔值表示。通常与toString方法的返回值相同。

#### 引用类型

基本类型和引用类型？

基本类型

* 是简单的数据段
* 不能给基本类型的值添加属性
* 基本类型保存在栈内存中

引用类型

* 指那些可能由多个基本类型构成的对象
* 引用类型值的变量实际上包含的并不是对象本身，而是一个指向该对象的指针
* 保存在堆内存中
	
###### 检测数据类型

基本数据类型

- typeof  （number,string,boolean,undefined,object,function）

对象

- instanceof  基本数据类型会返回false
- isPrototypeOf
- Object.prototype.toString.call

###### array 数组  
- 数组元素类型可不一致   //[1,'string']
- 可以设置数组的length来移除项或添加新项
- 检测数组
	- instanceof Array
	- Array.isArray() 方法
- 转换方法  // 输出字符串
	- toLocaleString  
	- toString  // alert的时候会调用
	- valueOf  
- 栈方法
	- push
	- pop
- 队列方法
 	- shift //弹出第一项
 	- unshift // 添加元素到队列头部
- 重排序方法
	- reverse
	- sort // 按toString后排序，按字符串比较 "10" < "5"  
	sort() 方法接受一个比较函数作为参数， 小于返回-1/ 大于返回1/ 等于返回0
- 操作方法
	- concat() 
	- slice()  // 截取数组的一段
	- splice() // 删除、插入、替换 ，返回的是删除的项
- 位置方法
	- indexOf
	- lastIndexOf
- 迭代方法
	- every // 检测是否每一项都使函数返回true
	- filter // 取出符合某函数的所有项
	- forEach  // 对每一项都执行某函数
	- map //返回每次函数调用的结果组成的数组
	- some // 检测是否存在项使得函数返回true
- 归并方法
	- reduce 从第一项开始遍历到最后
	- reduceRight 从最后一项遍历到第一项
 
	```
	 	var sum = arr.reduce(function(prev, current, index, array) {
	 		return prev + current;
	 	})
	```
	 
	两个参数：  
	 
		- 在每一项上调用的函数
		- 归并基础的初始值 
	  
	函数的四个参数：
	
		- 前一项值
		- 当前值
		- 项的索引
		- 数组对象
	函数的返回值会作为第一个参数自动传给下一项

###### Date
- 创建一个时间 //new Date();
- 获取毫秒数（以1970.1.1 的午夜为基准）
	- Date.parse("May 23, 2004")
	- Date.UTC(2015,4,5,13,55,33) // 年，月（0开始），日，时，分，秒
	- Date.now() //es5+
	- +new Date()
	- 日期的格式化方法
	- toDateString
	- toTimeString
	- toLocaleDateString
	- toUTCString
	- toLocaleString
	- tostring
- 日期的方法
	- getTime
	- getFullYear
	- getMonth
	- getDate
	- setFullYear
	- ...

###### RegExp 
```
  	var exp = /[a-z]\n/gi; 
  	var exp = new RegExp("[bc]at", "i");
  	- g: 全局模式 global
  	- i: 不区分大小写 case-insensitive
  	- m: 多行模式 multiline
```
 
采取构造函数的方式时，注意参数是字符串，所以转义的字面量表达式转成字符串需要双重转义。  

```
/\[bc\]at/   "\\[bc\\]at"
``` 

实例属性
 
- global 是否设置了g标志
- ignoreCase 是否设置了i标志
- lastIndex 表示开始搜索下一个匹配项的字符位置
- multiline 是否设置多行
- source 正则表达式字面量的字符串表示

实例方法
 
- exec(string) // 查找字符串
- test(string) // 检测是否有匹配  
  	
```
var text = "mom and dad and baby";
var pattern = /mom( and dad( and baby)?)?/gi;
var matches = pattern.exec(text);
``` 
结果为一个数组或null，
额外的属性： 
	
- index（匹配的起始位置）  
- input（匹配的字符串）属性
- 全局标志的g  
没有全局标志时，每次调用返回的内容相同，都是字符串第一个匹配项
设置全局标志，每次调用都会在字符串中继续查找新的匹配项  
   
构造函数的属性

- input //最近匹配的字符串
- lastMatch // 最近一次的匹配项
- lastParen // 最近一次匹配的捕获组
- leftContext // 匹配文本之前的内容
- rightContext // 匹配文本之后的文本


###### Function 类型
函数是对象，函数名实际是指向函数对象的指针。

 - arguments对象（传入的参数列表）  
   类数组对象，arguments的值与对应命名参数的值保持同步，但不是访问相同的内存空间。
   长度由传入的参数决定而不是定义的
 - 可用...扩展符来替代arguments, for...of来遍历

 ```
 function avg(...args) {
  	var sum = 0;
  	for (let value of args) {
    	sum += value;
  	}
  	return sum / args.length;
}
 ```
 
 - 返回值
  没有返回值的函数的返回的是undefined值 
 - 参数都是按值传递的
 - 检测是否是函数 typeof / Object.prototype.toString
 ```
 Object.prototype.toString.call(f) === "[object Function]"
 ```

- 变量提升  
  在代码执行前，会发生变量的函数声明提升，所以在函数声明前调用函数是可以的，函数表达式，则必须等到解析器执行到那时，才会被解释执行
- 函数属性  
	- arguments 保存函数参数
	  arguments.callee 指针，指向它所在的函数
	- this 引用的是函数的执行环境对象
	 
	 ```
	 window.color = "red";
	 var o = {color: "blue"};
	 function sayColor() {
	 	return this.color
	 }
	 sayColor(); // "red"
	 o.sayColor(); // "blue"
	 ```
	- arguments.caller 指向调用当前函数的函数，不能赋值
- 函数属性和方法
	- length // 希望接收的参数的个数
	- prototype 	
	
	- 在特定的作用域调用函数
	
		- apply(context, arguments) 第二个参数，可以是数组,可以是arguments对象
		- call(context, args[0], args[1],...) 依次列举所有的参数
	- 对象不需要与方法有任何的耦合
		- fn.bind(o)
   
	   	```
	   	function sayColor() {
	   			alert(this.color);
	   	}
	   	var objectSayColor = sayColor.bind(o); // 把this绑到了o上
	   	objectSayColor();
	   	```

#### 基本包装类型
javaScript提供的3种特殊的引用类型：Boolean，Number和String

```
var s1 = "some text";
var s2 = s1.substring(2);
```

第二行代码会有以下处理
1. 创建String类型的一个实例；
2. 在实例上调用指定的方法；
3. 销毁这个实例


* 引用类型与基本包装类型的生存期不同  
    引用类型的实例一直保存在内存中，直到执行流离开当前作用域。  
    而基本包装类型的对象，则在代码执行的瞬间产生，使用，然后销毁，所以我们不能在运行时为基本类型添加属性和方法
* 基本包装类型的实例  typeof会返回"object"，在布尔表达式中为true（new Boolean(false) == true）

```
var falseObject = new Boolean(false);
var result = falseObject && true; // true
var res = false && true; // false
```

* Object构造函数根据传入值的类型返回相应基本包装类型
* 基本包装类型的构造函数(e.g, new Number(value))与直接调用同名的转型函数(e.g, Number(value))不一样

###### Number类型
- toString() // 几进制
- toFixed()   // 小数
- toExponential() // 指数
...

###### String 
字符方法

- length 字符的个数
- charAt
- charCodeAt
- [] // str[1], es5+
字符串操作方法
- concat // 追加
- 找子串
	- slice(startIndex,endIndex)
	- substr(startIndex,num)
	- substring(startIndex,endIndex)
- 字符串位置方法
	- indexOf
	- lastIndexOf
- trim // 删除前后的空格，es5+
- 字符串大小写转换
	- toLowerCase
	- toUpperCase
	- toLocaleUpperCase 
	- toLocaleLowerCase 
- 字符串模式匹配的方法
   - match // str.match(pattern) <==> pattern.exec(str) 返回的是数组或null
   - search 返回的是第一个匹配项的索引或-1
   - repalce 查找并替换
   - split 字符串分割
- localeCompare  //比较字符串大小
- fromCharCode // 接收字符编码连成字符串

#### 单体内置对象
内置对象： Object,Array,String,Global,Math

###### global 
在全局定义的属性和函数都是Global对象的属性

- isNaN
- isFinite
- ...
- URI 编码方法
	- encodeURI // whole URI 只编空格
	- encodeURIComponent // part of URI，对非标准字符进行编码
	- decodeURI
	- decodeURIComponent
- eval  
 	可以执行字符串
	但是创建的变量和函数不会被提升
	只在执行时才创建
- 属性
	- undefined
	- NaN
	- Infinite
	- ...
- window对象
在Web浏览器中，全局作用域声明的对象和函数都称为了window对象的属性。全局对象是window对象的一部分。

###### Math
- 对象属性
	- Math.PI
	- Math.E
	- ...
- min(),max()
- 舍入方法
	- Math.ceil 向上
	- Math.floor 向下
	- Math.round 四舍五入
- random()
- ...
 
####  执行环境和作用域
###### 执行环境   
 分为全局执行环境和函数执行环境。
 - 每个函数都有自己的执行环境。执行环境定义了函数可以访问的数据。每进入一个执行环境，会创建一个作用域链。执行环境有助于确定何时释放内存。

###### 变量对象  
- 执行环境 <-> 变量对象
- 保存环境中定义的所有变量和函数

###### 环境栈  
执行流进入一个函数时，函数的环境就会被推入一个环境栈中，函数执行完毕后，栈弹出，把控制权返回给之前的执行环境。

###### 作用域链  
- 环境执行时， 会创建变量对象的一个作用域链
- 保证对执行环境有权访问的所有变量和函数的有序访问。  
- 作用域的前端，始终都是当前执行的代码所在环境的变量对象。
- 如果这个环境是函数，则将其活动对象作为变量对象。函数的活动对象最开始只包含arguments对象。下一个变量对象为包含环境，再下一个是下一个包含环境，一直延续到全局执行环境。

延长作用域链
创建新的变量对象，加入作用域链
- try-catch语句的catch块
- with 语句
变量的执行环境有助于确定应该何时释放内存

变量回收  

-  标记清除 	// 标记“进入环境”和“离开环境”
-  引用计数  	// 记录每个值的引用次数，循环引用时会出错  
 编码中注意解除变量的引用  
 
 
   	


   
 
  
  
  	
  	
  	
 


