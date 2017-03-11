### DOM 文档对象模型 
针对HTML和XML文档的一个API
#### 节点层次
DOM可以将HTML或XML文档描绘成一个由多层节点构成的结构。  
文档节点是每个文档的根结点。  
文档元素是最外层元素。

##### Node类型
12个类型

- ELEMENT_NODE(1)
- ATTRIBUTE_NODE(2)
- TEXT_NODE(3)
- CDATA_SECTION_NODE(4)
- ENITY_REFERENCE_NODE(5)
- ENITY_NODE(6)
- PROCESSING_INSTRUCTION_NODE(7)
- COMMENT_NODE(8)
- DOCUMENT_NODE(9)
- DOCUMENT_TYPE_NODE(10)
- DOCUMENT_FRAGMENT_NODE(11)
- NOTATION_NODE(12)

节点属性

- nodeType 类型 1～12
- nodeName
- nodeValue
- childNodes 子节点
childNodes 里保存着一个NodeList对象，类数组对象，保存一组有序的节点。  
访问NodeList: []或item() // some.childNodes.item(1);

```
//类数组对象转数组
function covertToArray(nodes) {
	var array = null;
	try {
	   	array = Array.prototype.slice.call(nodes, 0);
	} catch(ex) {
		array = new Array();
		for (var i = 0, len = nodes.length; i < len; i++) {
			array.push(nodes[i]);
		}
	}
	return array;
}
```
节点关系的属性

- parentNode 父节点
- previousSibling
- nextSibling
- firstChild
- lastChild

查找方法

- hasChildNodes

```
                 ______ 
                | node |
                |______|
firstChild/         |       \ lastChild
        /           |          \
      /             |            \
    /               | parentNode   \ 

  /  nextSibling(->)                 \
________        ________        ________    
| node | -----> | node | -----> | node |
|______| <----- |______| <----- |______|
                    previousSibing(<-)

```
操作节点：

- appendChild 放到childNodes末尾
- insertBefore 放在某节点之前
- replaceChild
- removeChild
- cloneNode
- normalize 去除空文本，合并相邻文本

##### Document类型
用Document类型表示文档。  

子节点  

- DocumentType
- Element
- ProcessingInstruction
- Comment

文档信息 
 
- document.title
- document.URL
- document.domain
- document.referrer

查找元素 返回的是HTMLCollection

- getElementById
- getElementsByTagName
- getElementByName // 仅限HTMLDocument

特殊集合

- document.anchors // 带name的a
- document.applets
- document.forms
- document.images
- document.links 

DOM 一致性检测
document.implementation.hasFeatrue("XML", "1.0")

文档写入

- write()
- writeln()
- open()
- close()

##### Element类型
元素属性

- tagName // nodeName
- id
- title
- className
- lang
- dir

取得属性

- getAttribute // style 和 click 会与直接属性访问不同
- setAttribute
- removeAttribute

attributes 属性
包含一个NamedNodeMap，有以下方法

- getNamedItem(name)
- removeNamedItem(name)
- setNamedItem(node)
- item(pos)

创建元素

- document.createElement("div")

##### Text 类型
没有子节点
操作文本

- appendData
- deleteData
- insertData
- replaceData
- splitText
- substringData
- length 

其他方法 

- document.createTextNode()  创建文本节点
- normalize() 规范化文本节点--合并文本
- splitText() 分割文本，返回剩下的文本

##### Comment
属性或方法

- data // nodeValue 获取注释的内容
- createComment()

##### CDATASection
只基于XML文档

##### DocumentType
包含与文档doctype有关的所有信息

- name
- entities
- notations

##### DocumentFragmment
文档片段， 当作“仓库”来使用，包含一些其他节点

##### Attr
创建特性节点

- createAttribute()

### DOM 操作技术

##### 动态脚本

```
// 动态加载外部脚本
function loadScript(url) {
	var script = document.createElement("script");
	script.type = "text/javascript";
	script.src = url;
	document.body.appendChild(script);
{

// 加载代码
function loadScriptString(code) {
	var script = document.createElement("script");
	script.type = "text/javascript";
	try {
		script.appendChild(document.createTextNode(code));
	} catch (ex) {
		script.text = code;
	}
	document.body.appendChild(script);
}
```

##### 动态样式

```
// 动态加载样式表
function loadStyles(url) {
	var link = document.createElement("link");
	link.rel = "stylesheet";
	link.type = "text/css";
	link.href = url;
	var head = document.getElementsByTagName("head")[0];
	head.appendChild(link);
}

// 设置样式
function loadStyleString(css) {
	var style = document.createElement("style");
	style.type = "text/css";
	try {
		style.appendChild(document.createTextNode(css));
	} catch (ex) {
		style.styleSheet.cssText = css;
	}
	var head = document.getElementsByTagName("head")[0];
	head.appendChild(style);	
}
```

##### 使用 NodeList
NodeList,NamedNodeMap,HTMLCollectin 是动态的，随文档结构变化而变化。

### DOM 扩展
#### 选择符API

- querySelector("#mydiv") // 返回匹配的第一个元素或null
- querySelectorAll() // 返回的是符合的所有元素
- matchesSelector() // 返回是否匹配某个规则

#### 元素遍历
对元素空格，不同浏览器的childNodes和firstChild等属性的行为不一致，提出下列的属性

- childElementCount 
- firstElementChild  
- lastElementChild
- previousElementSibling
- nextElementSibling

#### HTML5
##### 与类相关的扩充

- getElementsByClassName()
- classList
	- add
	- contains
	- remove
	- toggle

##### 焦点管理

- document.activeElement // 当前获得焦点的元素
- someElement.focus() // 获取焦点
- document.hasFocus() // 确定文档是否获得焦点

##### HTMLDocument的变化
- readyState 属性
	- "loading" 正在加载文档
	- "complete" 已经加载完了文档
- 兼容模式
IE6开始区分渲染页面的模式是标准的，还是混杂的
	
	- document.compatMode // 渲染页面的模式
		- CSS1Compat 标准模式
		- BackCompat 混合模式
- head属性
	- document.head

##### 字符集属性
- document.charset 文档实际使用的字符集
- document.defaultCharset 浏览器及操作系统的默认设置

##### 自定义数据属性 data-

```
<div id="myDiv" data-appId="123" data-myName="nocholos"></div>

var appId = div.dataset.appId;
var myName = div.dataset.myName;

```

##### 插入标记

- innerHTML 内部的内容
- outerHTML 读，返回调用它的元素及所有子节点的HTML标签；写，根据内容构建新的DOM子数，然后用这个DOM子树替换调用元素。
- insertAdjacentHTML()
	- beforebegin // 当前元素之前插入一个紧邻的同辈元素
	- afterbegin  // 当前元素之下插入新的子元素
	- beforeend  // 当前元素之下插入新的子元素
	- afterend //当前元素之后插入一个紧邻的同辈元素
- 内存与性能问题  
元素替换之前，删除被替换的元素的所有事件处理程序和JavaScript对象属性。  
innerHTML会创建一个HTML解析器。不要频繁修改它。

##### scrollIntoView
   	
#### 专有扩展

##### 文档模式
文档模式决定了你可以使用哪个级别的CSS，可以在JavaScript使用哪些API，如何对待文档类型doctype。

- IE5 混杂模式
- IE7 IE7标准模式
- IE8 IE8标准模式
- IE9 IE9标准模式

##### children属性
HTMLCollection的实例，包含元素中同样还是元素的子节点。

##### contains() 方法
检测一个节点是否是另一个节点的后代。  
compareDocumentPosition() 确定节点的关系。

##### 插入文本

- innerText  / textContent
- outerText

##### 滚动

- scrollIntoViewIfNeeded
- scrollByLines
- scrollByPages

### DOM2 和 DOM3

##### 命名空间的变化
Node类型和其他类型多了一些命名空间的属性和方法

##### 其他方面变化

- DocumentType 类型的变化
	- publicId
	- systemId
	- internalSubset 额外的信息
- Document 类型
	- importNode() 导入别的文档的节点
	- defaultView
	- document.implementation.createDocumentType
	- document.implementation.createDocument
	- document.implementation.createHTMLDocument
- Node 类型
	- isSupported("HTML", "2.0") 支持什么能力
	- isSameNode()
	- isEqualNode()
	- setUserData()/getUserData() 
- 框架的变化
	- contentDocument 框架内容的文档对象

#### 样式
style对象的属性

- cssText
- parentRule
- getPropertyCSSValue
- getPropertyPriority
- getPropertyValue
- removeProperty
- setProperty

计算的样式

- getComputedStyle // IE 不支持，IE用currentStyle

##### 操作样式表
- stylesheets
	- cssRules 
	- insertRule()
	- deleteRule()
	- ...

##### 元素大小
- 偏移量
	- offsetHeight 含边框
	- offsetWidth
	- offsetLeft 元素左外边框至包含元素的左内边框的距离
	- offsetTop 元素的上外边框至包含元素的上内边框的距离
- 客户区的大小
元素内容机器内边距所占空间的大小。不含边框
	- clientHeight
	- clientWidth
- 滚动大小
	- scrollHeight 没有滚动条的情况下，元素内容的总高度
	- scrollWidth 没有滚动条的情况下，元素内容的总宽度
	- scrollLeft 
	- scrollTop
- 确定元素大小
	- getBoundingClientRect() 给出元素在页面中相对于视口的位置。

#### 遍历
深度优先遍历

- NodeIterator
	- createNodeIterator(root, whattoshow, filter, entityReferenceExpansion)

```
	var div = document.getElementById("div1");
	var filter = function(node) {
		return node.tagName.toLowerCase == "li" ? NodeFilter.FILTER_ACCEPT: NodeFilter_SKIP;
	}
	var iterator = document.createNodeIterator(div, NodeFilter.SHOW_ELEMENT, filter, false);
	var node = iterator.nextNode();
	while (node != null) {
		alert(node.tagName);
		node = iterator.nextNode();
	}
```

- TreeWalker
	- parentNode
	- firstChild
	- lastChild
	- nextSibling
	- previousSibling
	- createTreeWalker
	- currentNode
强大之处在于能够在DOM结构中沿任何方向移动

```
var div = document.getElementById("div");
var walker = document.createTreeWalker(div, NodeFilter.SHOW_ELEMENT, null, false);
walker.firstChild();
walker.nextSibling();

var node = walker.firstChild();
while (node !== null) {
	alert(node.tagName);
	node = walker.nextSibling();
}
```

#### 范围 （IE9）

##### DOM中的范围

- createRange()
- selectNode()
- selectNodeContents()
- startContainer 范围起点
- startOffset  范围在startContainer中起点的偏移量
- endContainer 包含范围终点的节点
- endOffset
- commonAncestorContainer 
- setStartBefore 范围的起点设置在refNode之前
- setStartAfter 
- setEndBefore 范围的终点设置在refNode之前
- setEndAfter

##### 用DOM范围直接实现复杂选择
- setStart()
- setEnd() 

```
var range1 = document.createRange();
var range2 = document.createRange();
var p1 = document.getElementById("p1");
var p1Index = -1;
var i, len;
for (i = 0; i < p1.parentNode.childNodes.length; i++) {
	if (p1.parentNode.childNodes[i] == p1) {
		p1Index = i;
		break;
	}
}

range1.setStart(p1.parentNode, p1Index);
range1.setEnd(p1.parentNode, p1Index + 1);
range2.setStart(p1, 0);
range2.setEnd(p1, p1.childNodes.length);
```

##### 操作DOM范围中的内容
- deleteContents()
- extractContents()
- cloneContents()
- insertNode()
- surroundContents() 新节点作为范围的父节点插入到原来范围的位置上
- collapse() 折叠范围，检测是否有空隙
 
 
##### DOM范围操作
- compareBoundaryPoints() 比较范围
- cloneRange() 复制范围
- detatch() 清理范围
