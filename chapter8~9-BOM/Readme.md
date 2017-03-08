### BOM  浏览器对象模型

#### window
- 是JavaScript访问浏览器窗口的接口
- 也是ECMAScript规定的Global对象

##### 全局作用域
全局作用域里声明的变量，函数会成为window对象的属性和方法。
但是还是有点差别，window的属性可以用delete删除，但是全局变量不能

```
var age = 29;
window.color = "red";

delete window.age; // false
delete window.color; // true
```

##### 窗口关系及框架

每个框架都拥有自己的window 对象，并且保存在frames集合中。

```
<frameset rows="160,*">
	<frame src="" name="topframe">  // window.frames[0]/window.frames['topframe']
	<frameset>
		<frame src="" name="leftframe"> // window.frames[1]
		<frame src="" name="rightframe"> // window.frames[2]
	</frameset>
</frameset>
```

- top
  始终指向最外层的框架，也就是浏览器窗口top.frames[0],top.frames[1]  
- parent，parent对象始终指向当前框架的直接上层框架
- self, 始终指向window
- 以上都是window的属性 // window.top, window.parent, window.self

##### 窗口位置

- screenLeft // 相对屏幕左边的位置  IE,safari,opera, chrome
- screenTop // 相对屏幕上边的位置   
- screenX //FF, safari, chrome
- screenY  
- moveTo
- moveBy

##### 窗口大小

- innerWidth  // 页面视图容器的大小
- innerHeight
- outerWidth // 返回浏览器窗口本身的尺寸
- outerHeight  
DOM方法
- document.documentElement.clientWidth
- document.body.clientHeight
- resizeTo
- resizeBy

##### 导航和打开窗口

window.open

- 第二个参数
	- _self
	- _parent
	- _top
	- _blank 	
- 第三个参数
http://www.w3school.com.cn/jsref/met_win_open.asp#windowfeatures

返回的是新窗口的引用
window.open打开的弹出窗口，可以用window.close关闭  
window.opener 打开它的原始窗口对象  

弹出窗口屏蔽程序

```
var blocked = false;

try {
	var wroxWin = window.open("http://www.wrox.com", "_blank");
	if (wroxWin == null) {
		blocked = true;
	}
} catch(ex) {
	blocked = true;
}

if (blocked) {
	alert("the popup was blocked!");
}
```

##### 间歇调用和超时调用

* 超时调用 setTimeout  

```
// 不建议传递字符串
setTiemout("alert('hi')", 1000); 

// setTimeout(function() {
	alert("hello world!");
}, 1000);
```
JavaScript是单线程的，有一个任务队列。  
setTimeout的第二个参数，是告诉JavaScript再过多久把当前任务添加到队列中。  
如果队列是空的，会立即执行，否则要等前面的都执行完毕之后再执行。

任务队列：http://www.ruanyifeng.com/blog/2014/10/event-loop.html

* 取消超时调用
setTimeout会返回一个ID，是执行代码的唯一标识符。

```
// 设置超时调用
var timeoutId = setTimeout(function() {
	alert("hi");
}, 1000);

// 取消
clearTimeout(timeoutId);
```

* 间歇调用 setInterval
* 取消间歇调用 clearInterval
* 间歇调用一般可以用超时调用完成，因为后一次的间歇调用可能会在前一个间歇调用结束之间启动，最好别用间歇调用

##### 系统对话框
 - alert // ok按钮
 - confirm // cancle(return false) and ok(return true) 
 - prompt // 输入文本，OK cancle ok返回文本，cancle返回null
 - print // 打印对话框
 - find // 查找对话框
 
#### location 对象

提供与当前窗口加载的文档有关信息，还把URL解析为独立的片段。

- hash // "#contents"
- host // "www.baidu.com:80"
- hostname // "www.baidu.com"
- href // "http://www.baidu.com"
- pathname // "/wileycda/"
- port // "8080"
- protocol "http:"
- search "?qt=123"

查询字符串参数

```
function getQueryStringArgs() {
	// 取得查询字符串并去掉开头的问号
	var qs = location.search.length > 0 ? location.search.substring[1] : "",
	// 保存数据的对象
	args = {},
	
	// 取得每一项
	items = qs.length ? qs.split("&"): [],
	item = null,
	name = null,
	value = null,
	
	i = 0,
	len = items.length;
	for (i = 0; i < len; i++) {
		item = items[i].split("=");
		name = decodeURIComponent(item[0]);
		value = decodeURIComponent(item[1]);
		
		if (name.length) {
			args[name] = value;
		}
	}
	return args;
}
```
* 位置操作 // 改变浏览器的位置  
`assign` // location.assign("http://www.wrox.com");  
window.location = "http://www.wrox.com"  
window.href = "http://www.wrox.com";  
以上修改，会在浏览器的历史记录中生成一条新记录，即可以“回退”到前一页  
`location.replace()` 不能回退  
`reload` 重新加载当前页面

```
location.reload(); // 可能从缓存中加载
location.reload(true); // 从服务器重新加载
```
#### navigator 对象
识别客户端浏览器  

- 属性
	- appCodeName 浏览器名称 // Mozilla
	- appName 完整的浏览器名称
	- appVersion 浏览器的版本
	- platform 浏览器所在的系统平台
	- userAgent 浏览器的用户代理字符串
	- ...  
- 检测插件

```
// 检测插件 (非IE)
function hasPlugin(name) {
	var name = name.toLowerCase();
	for (var i = 0; i < navigator.plugins.length; i++) {
		if (navigator.plugins[i].name.toLowerCase.indexOf(name) > -1) {
			return true; 
		}
	}
	return false;
}

// IE 
function hasIEPlugin(name) {
	try {
		new ActiveXObject(name);
		return true;
	} catch (ex) {
		return false;
	}
}
```

- 注册处理程序  
让站点注明它可以处理特定类型的信息。
	- registerContentHandler
	- registerProtocolHandler
demo:

```
  navigator.registerContentHandler("application/rss + xml", "htpp://www.somereader.com", "some reader");
```


#### screen 对象
表明客户端的能力，包括浏览器窗口外部的显示器的信息，如像素和高度等。一般用于站点分析。
- availHeight
- availWidth
- ...

#### history 对象
- go 跳转 

```
// 后退一页
history.go(-1); // history.back();

// 前进一页
history.go(1); // hisroty.forward();

// 跳转到最近的wrox.com页面
history.go("wrox.com");


```


### 客户端检测
针对浏览器的差异，我们设计时应该用最通用的能力方法，然后再针对特定浏览器提供增强方案。
##### 能力检测
 检测浏览器是否支持特定的能力
 
 ```
 if (object.propertyInQuestion) {
 	// use object.propertyInQuestion;
 }
 ```

- 更可靠的能力检测

```
// 检测sort是不是函数
function isSortable(object) {
	return typeof object.sort == 'function';
}
```

注意：宿主对象没有义务让typeof 返回合理的值。

typeof document.createElement 为 object // IE8及之前
 
```
function isHostMethod(object, property) {
	var t = typeof object[property];
	return t == 'function' || (!! (t == 'object' && object[property])) || t == 'unknown'
}
```

##### 怪癖检测
识别浏览器的特殊行为，知道浏览器存在什么缺陷／bug

##### 用户代理检测
检测实际使用的浏览器,

```
var client = function() {

   // 呈现引擎
	var engine = {
		ie: 0,
		gecko: 0,
		webkit: 0,
		khtml: 0,
		opera: 0,
		// 版本号
		ver: null
	};

	// 浏览器
	var browser = {
		ie: 0,
		firefox: 0,
		safari: 0,
		konq: 0,
		opare: 0,
		chrome: 0,
		// 版本号
		ver: null
	};
	// 平台
	var system = {
		win: false,
		mac: false,
		x11: false, // unix
		// 移动设备
		iphone: false,
		ipod: false,
		ipad: false,
		ios: false,
		android: false,
		// nikiaN: false,
		winMobile: false,
		// 游戏系统
		wii: false,
		ps: false
	};

	// 检测呈现引擎和浏览器
	var ua = navigator.userAgent;
	if (window.opera) {
		engine.ver = browser.ver = window.opera.version();
		engine.opera = browser.opera =  parseFloat(engine.ver);
	} else if (/AppleWebKit\/(\S+)/.test(ua)) {  // \S 为空字符
		engine.ver = RegExp["$1"];
		engine.webkit = parseFloat(engine.ver);
		// 确定是chrome还是safari
		if (/Chrome\/(\S+)/.test(ua)) {
			browser.ver = RegExp["$1"];
			browser.chrome = parseFloat(browser.ver);
			// Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36"
		} else if (/Version\/(\S+)/.test(ua)) {
			browser.ver = RegExp["$1"];
			browser.safari = parseFloat(browser.ver);
			// "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/602.4.8 (KHTML, like Gecko) Version/10.0.3 Safari/602.4.8"
		} else {
			// 近似地确定版本号
			var safariVersion = 1;
			if (engine.webkit < 100) {
				safariVersion = 1;
			} else if (engine.webkit < 312) {
				safariVersion = 1.2;
			} else if (engine.webkit < 412) {
				safariVersion = 1.3;
			} else {
				safariVersion = 2;
			}
			browser.safari = browser.ver = safariVersion;
		}
	} else if (/KHTML\/(\S+)/.test(ua) || /Konqueror\/([^;]+)/.test(ua)) {
		engine.ver = RegExp["$1"];
		engine.khtml = parseFloat(engine.ver);
	} else if (/rv:([^\)]+)\) Gecko\/\d{8}/.test(ua)) {
		engine.ver = RegExp["$1"];
		engine.gecko = parseFloat(engine.ver);
		// 确定是不是firefox
		if (/Firefox\/(\S+)/.test(ua)) {
			browser.ver = RegExp["$1"];
			browser.firefox = parseFloat(browser.ver);
		}
		// "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.12; rv:50.0) Gecko/20100101 Firefox/50.0"
	} else if (/MSIE ([^;]+)/.test(ua)) {
		engine.ver = RegExp["$1"];
		engine.ie = parseFloat(engine.ver);
	}
	
	// 检测浏览器
	browser.ie = engine.ie;
	browser.ipera = engine.opera;

	// 检测平台
	var p = navigator.platform;
	system.win = p.indexOf("Win") == 0;
	system.mac = p.indexOf("Mac") == 0;
	system.x11 = (p.indexOf("X11") == 0) || (p.indexOf("Linux") == 0) ;


	// 检测windows操作系统
	if (system.win) {
		if (/Win(?:dows)?([^do]{2})\s?(\d+.\d+)?/.test(ua)) {
			if (RegExp["$1"] == "NT") {
				switch(RegExp["$2"]) {
					case "5.0": 
						system.win = "2000";
						break;
					case "5.1":
						system.win = "XP";
						break;
					case "6.0": 
						system.win = "Vista";
						break;
					case "6.1":
						system.win = "7";
						break;
					default:
						system.win = "NT";
						break;
				}
			} else if(RegExp["$1"] == "9x") {
				system.win = "ME";
			} else {
				system.win = RegExp["$1"];
			}
	}

	// 移动设备
	system.iphone = ua.indexOf("iPhone") > -1;
	system.ipod = ua.indexOf("iPod") > -1;
	system.ipad = ua.indexOf("iPad") > -1;
	
	// winodws mobile
	if (system.win == 'CE') {
		system.winMobile = system.win;
	} else if (system.win == "Ph") {
		if (/Windows Phone OS (\d+_\d+)/.test(ua)) {
			system.win = "Phone";
			system.winMobile = parseFloat(RegExp["$1"]);
		}
	}

	// 检测ios版本
	if (system.mac && ua.indexOf("Mobile") > -1) {
		if (/CPU(?:iPhone )?OS (\d+_\d+)/.test(ua) {
			system.ios = parseFloat(RegExp.$1.replace("_","."));
		} else {
			system.ios = 2; // 猜测版本
		}
	}
	
	// 检测Android版本
	if (/Android (\d+\.\d+)/.test(ua)) {
		system.android = parseFloat(RegExp.$1);
	}

	// 游戏系统
	system.wii = ua.indexOf("Wii") > -1;
	system.ps = /playstation/i.test(ua);

	return {
		engine: engine,
		browser: browser,
		system: system
	};
};
```