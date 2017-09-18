### 事件
JavaScript 与 HTML 之间的交互通过事件实现。  

##### 事件流
事件流描述的是从页面中接收事件的顺序。  

- 事件冒泡 // 逐级向上 
- 事件捕获 // 依次向下
- DOM事件流
    - 事件捕获阶段 // 1,2,3
    - 处于目标阶段 
    - 事件冒泡阶段 // 4,5,6,7

```
   1  Document        7
   |  |                 \
   2  |___ Element html   6
   |       |                \
   3       |___ Element body 5
                |               \ 
                |___ Element div  4
```

#### 事件处理程序
##### HTML事件处理程序
用HTML特性来指定事件处理程序。
- onclick
- event
通过event变量，可以直接访问事件对象，this值为事件的目标元素

```
<input type="button" value="click me" onclick="showMessage()" />
<input type="button" value="click me" onclick="alert(event.type)" />
<input type="button" value="click me" onclick="alert(this.value)" />
```

##### DOM0级事件处理程序
```
var btn = document.getElementById('mybtn');
btn.onclick = function() {
    alert(this.id);
}

// 删除事件处理程序
btn.onclick = null;
```

##### DOM2级事件处理程序
- addEventListener()
- removeEventListener() 事件处理程序函数必须和addEventListener中的是同一个

```
var btn = document.getElementById("mybtn");
var handler = function() {
    alert(this.id);
}

btn.addEventListener("click", handler, false);  // true为捕获，false为冒泡
// 若有多个事件，按序执行
btn.removeEventListener("click", handler, false); 
```

##### IE事件处理程序
- attachEvent()
this === window，多个事件，逆序执行
- detachEvent()

```
var btn = document.getElementById("mybtn");
var handler = function() {
    alert(this.id);
}

btn.attachEvent("onclick", handler);  // true为捕获，false为冒泡
// 若有多个事件，按序执行
btn.detachEvent("onclick", handler); 
```

#### 事件对象

##### DOM中的事件对象
DOM 浏览器会将一个event对象传入到事件处理程序中。  
event对象的成员

- bubbles       // 是否冒泡
- canclable     // 取消事件的默认行为
- currentTarget // 当前处理事件的元素，可能为实际目标的父节点
- eventPhase    // 调用事件处理程序的阶段，1: 捕获；2: 目标对象；3: 冒泡
- preventDefault() // 阻止事件的默认行为，canclable为true时生效
- stopImmediatelyPropagation() // 取消事件的进一步捕获或冒泡，同时阻止任何事件处理程序被调用
- stopPropagation() // 取消事件的进一步捕获或冒泡
- target        // 事件的目标
- type          // 事件的类型 
- ...

this 为 currentTarget的值，target为事件的实际目标

##### IE中的事件对象
event对象作为window对象的一个属性。

```
var btn = document.getElementById('mybtn');
btn.onclick = function () {
    var event = window.event;
    alert(event.type);
}
```

如果使用attachEvent()，则会有一个event对象作为参数传入事件处理程序函数中。

```
var btn = document.getElementById('mybtn');
btn.attachEvent("onclick", function(event) {
    alert(event.type);
})
```

IE 的 event 对象的属性

- cancleBubble // 是否取消事件冒泡 true/false
- srcElement    // 事件的目标
- type          // 事件的类型
- returnValue   // 是否取消事件的默认行为

##### 跨浏览器的事件对象
```
var EventUtil = {
    addHandler: function(element, type, handler) {
        if (element.addEventListener) {
            element.addEventListener(type, handler, false);
        } else if (element.attachEvent) {
            element.attachEvent("on" + type, handler);
        } else {
            element["on" + type] = handler;
        }
    }, 

    getEvent: function(event) {
        return event? event: window.event;
    },

    getTarget: function(event) {
        return event.target? event.target: event.srcElement; 
    },

    preventDefault: function(event) {
        if (event.preventDefault) {
            event.preventDefault();
        } else {
            event.returnValue = false;
        }
    },

    removeHandle: function(element,type ,handler) {
        if (element.removeEventListener) {
            element.removeEventListener(type, handler, false);
        } else if (element.detachEvent) {
            element.detachEvent(type, handler, false);
        } else {
            element["on" + type] = null;
        }
    },

    stopPropagation: function(event) {
        if (event.stopPropagation) {
            event.stopPropagation();
        } else {
            event.cancleBubble = true;
        }
    }
}
```

#### 事件类型
- UI事件 
- 焦点事件
- 鼠标事件
- 滚轮事件
- 文本事件
- 键盘事件
- 合成事件 // 当为输入法编辑器输入字符时触发
- 变动事件 // DOM结构发生变化时触发

##### UI事件
- load 页面／框架／图像／潜入的内容在加载完毕后触发
- unload 卸载后触发
- abort 在用户停止下载过程时，如果嵌入的内容没加载完，在<object>元素上触发
- error JS错误时在window上触发，或无法加载图像／嵌入内容／框架时触发
- select 用户选择文本框中的字符
- resize 当窗口或框架的大小变化时触发
- scroll

load 事件  
页面完全加载后，包括图像，JS文件，CSS文件等外部资源  

unload 事件  
用户从一个页面切换到另一个页面，就会发生unload事件  

resize  
不同浏览器的机制不同，有的会不断重复触发，FF只在用户停止调整窗口大小时才会触发，所以不要在这个事件的处理程序中加入大计算量的代码。  

##### 焦点事件
- blur 失去焦点时触发，不冒泡
- focus 获得焦点时触发，不冒泡
- focusin 获得焦点时触发，冒泡
- focusout 失去焦点时触发，冒泡

焦点从页面中的一个元素跳到另一个元素的过程
 - focusout
 - focusin
 - blur
 - focus

##### 鼠标与滚轮事件
- click
- dbclick
- mousedown
- mouseenter  // 在鼠标光标移动到元素范围内触发
- mouseleave  // 光标移动到元素范围外，后代元素不生效
- mousemove   // 鼠标指针在元素内部移动时重复地触发
- mouseout    // 鼠标指针位于一个元素上方，然后用用户将其移入另一个元素时触发，可以是后代元素 
- mouseover   // 元素外部移入到另一个元素边界之内
- mouseup    

客户端坐标位置  
鼠标光标在视口中的位置 clientX 和 clientY  

页面坐标位置  
鼠标光标在页面中的位置 pageX 和 pageY

屏幕坐标位置  
鼠标光标在屏幕中的位置 screenX 和 screenY

修改键  
某些键的状态会影响到所要采取的操作。  
- event.shiftKey
- event.ctrlKey
- event.altKey
- event.metaKay (windows / cmd)

相关元素  
mouseover 和 mouseout 事件，从一个元素到另一个元素，一个是主目标，另一个就是相关元素。  
relatedTarget属性保存了相关元素的信息。

鼠标按钮  
event.button 保存了鼠标按下的哪些按钮（主（左），次（右），中）

鼠标滚轮事件  
mousewheel 

触摸设备
不支持dbclick；  
轻击元素会触发mousemove事件；  
mousemove 会触发mouseover 和 mouseout  
两个手指移动会触发mousewheel 和 scroll 事件  

##### 键盘与文本事件
键盘文件

- keydown
- keypress  //  文本框变化前触发
- keyup     //  文本框变化后触发

文本事件
- textInput

键码 
- event.keyCode // 13
- event.charCode
- event.key // "k"
- event.char

textInput 事件
- event.data 就是用户输入的字符
- event.inputMethod 文本输入到文本框中的方式

##### 复合事件
- compositionstart // 在IME的文本系统打开时触发 
- compositionupdate // 在向输入字段中插入新字符时触发
- compositionend // 系统关闭

##### 变动事件
- DOMSubtreeModified 在DOM结构发生变化时触发
- DOMNodeInserted 在一个节点被插入到另一个节点中触发
- DOMNodeRemoved 在节点从其父节点中被移除时触发
- DOMNodeInsertedIntoDocument
- DOMNodeRemovedFromDocument
- DOMAttrModified 
- DOMCharacterDataModified

删除节点  
removeChild() 或 replaceChild() 会触发 DOMNodeRemoved，DOMNodeRemovedFromDocument ,子节点会触发 DOMNodeRemovedFromDocument，然后触发 DOMSubtreeModified事件。

插入节点  
appendChild(), replaceChild() , insertBefore() 会触发DOMNodeInserted 。会在上面触发DOMNodeInsertedIntoDocument事件。最后触发DOMSubtreeModified。

##### HTML5事件
- contextmenu 事件 右键出现上下文菜单
- beforeunload事件 卸载页面之前
- DOMContentLoaded 形成完整的DOM树后触发，不考虑资源是否加载完毕
- readystatechange事件 文档状态相关
    - readyState
    - uninitialized（未初始化）
    - loading
    - loaded
    - interactive
    - complete
- pageshow 和 pagehide 事件
“往返缓存” 把整个页面保存在内存里。如果页面位于bfcache(back-foward cache)，再次打开该页面时都不会触发load事件。在load之后会触发pageshow。
- hashchange 事件 URL参数列表变化时触发

##### 设备事件
- orientationchange 设备方向-- 横向纵向 Safari浏览器
- MozOrientation x(左右倾斜),y(前后倾斜),z(垂直加速度)
- deviceorientation 设备方向
- devicemotion 设备移动的加速度

##### 触摸与手势事件
触摸事件  
- touchstart 
- touchmove
- touchend
- touchcancel

触摸属性
- touches
- targetTouches
- changeTouches
- clientX
- clientY
- identifier
- pageX
- pageY
- screenX
- screenY
- target

顺序  
- touchstart
- mouseover
- mousemove
- mousedown
- mouseup
- click
- touchend

手势事件  
- gesturestart 一个手指在屏幕上，另一个手指又触摸屏幕时触发
- gesturechange
- gestureend

属性
- rotation
- scale

#### 内存和性能
- 事件委托
事件委托利用了事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。
- 移除事件处理程序

#### 模拟事件
##### DOM 中的事件模拟
- createEvent("MouseEvents") // 创建事件对象
- initEvent(type,bubbles...) // 初始化事件对象
- dispatchEvent(event)   // 触发事件

##### IE中的事件模拟
- createEventObject() 返回通用的event，用户去设置属性







































