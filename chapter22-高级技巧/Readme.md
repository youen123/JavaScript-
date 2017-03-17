### 高级技巧
#### 高级函数
##### 安全的类型检测
typeof --> instance of --> Object原生的toString()方法
Object.prototype.toString.call(value) // "[object Array]"

```
// 判断是否是数组
function isArray(value) {
    return Object.prototype.toString.call(value) == "[object Array]"; 
}

// 判断是否是函数
function isFunction(value) {
    return Object.prototype.toString.call(value) == "[object Function]";
}

function isRegExp(value) {
    return Object.prototype.toString.call(value) == "[object RegExp]";
}
```

##### 作用域安全的构造函数
忘记new操作符，导致this指向window，给window加上了不必要的属性

```
function Person(name, age, job) {
    if (this instanceof Person) {
        this.name = name;
        this.age = age;
        this.job = job;
    } else {
        return new Person(name, age, job);
    }
}
```

##### 惰性载入函数
为了少走if

```
function createXHR() {
    if (typeof XMLHttpRequest != "undefined") {
        createXHR = function() {
        
        }
    } else {
        createXHR = function() {
        
        }
    }
    return createXHR();
}
```

```
var createXHR =(function() {
    if (typeof XMLHttpRequest != "undefined") {
        return function() {
            
        }
    } else {
        return function() {
        
        }
    }

})();
```

##### 函数绑定

```
var handler = {
    message: "Event handled",

    handleClick: function(event) {
        alert(this.message + ":" + event.type);
    }
}

var btn = document.getElementById("btn");
EventUtil.addHandler(btn, "click", handler.handleClick.bind(handler));
```

##### 函数柯里化
通过简单的传递几个参数，就能动态创建实用的新函数。

```
function curry(fn) {
    var args = Array.prototype.slice.call(arguments, 1);
    return function() {
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return fn.apply(null,finalArgs);
    }
}
```

```
function add(num1,num2) {
    return num1 + num2;
}

var curriedAdd = curry(add, 5);
alert(curriedAdd(3)); // 8
```

#### 防篡改对象
防篡改不可逆
##### 不可扩展对象
- Object.preventExtensions(person) 不能给对象新增属性方法，可修改已有的属性方法
- Object.isExtensible(person) 确定对象是否可扩展

##### 密封的对象
- Object.seal(person) 不可扩展，也不能删除属性和方法。 [[Configurate]]为false
- Object.isSealed(person) 确定对象是否被密封

##### 冻结对象
- Object.freeze(person) 是密封的，同时对象的属性不可变。[[Writable]]为false
- Object.isFrozen() 确定对象是否冻结

#### 高级定时器
定时器：在特定事件后将代码插入到任务队列中。

* 数组分块

把长时间运行的脚本切分为一小块一小块来运行。

```
function chunk(array, process, context) {
    setTimeout(funciton() {
        var item = array.shift();
        process.call(context, item);
        if (array.length > 0) {
           setTimeout(arguments.callee, 100);
        }
    }, 100);
}
```

* 函数节流

思想：某些代码不可以在没有间断的情况连续连续重复执行。第一次调用时，创建一个定时器，在指定事件间隔之后运行代码，当第二次调用该函数时，它会清除前一次的定时器并设置另一个。目的是在执行函数的请求停止一段事件之后才执行。

```
var processor = {
    timeoutId: null,
    
    // 实际进行处理的方法 
    performProcessing: function() {
    
    },
    
    // 初始处理调用的方法
    process: function() {
        clearTimeout(this.timeoutId);

        var that = this;
        this.timeoutId = setTimeout(function() {
            that.performProcessing();
        }, 100)
    }
}
```
简化为

```
function throttle(method. context) {
    clearTimeout(method.id);
    method.id = setTimeout(function() {
        method.call(context);
    }, 100)
}
```

如resize的例子

```
// 原先 耗资源
window.onresize = function() {
    var div = document.getElementById("mydiv");
    div.style.height = div.offsetWidth + "px";
};

// 改进
function resizeDiv() {
    var div = document.getElementById("mydiv");
    div.style.height = div.offsetWidth + "px";
}

window.onresize = function() {
    throttle(resizeDiv);
}
```

#### 自定义事件
事件是一种叫做观察者的设计模式。  
观察者模式由两类对象组成：主体和观察者。 
 
- 主体  
  负责发布事件，不知道观察者的任何事情。
- 观察者  
  通过订阅这些事件来观察该主体，知道主体，并能注册事件的回调函数。

自定义事件有助于将不同部分的代码相互之间解耦。

```
function EventTarget = {
    this.handlers = {}
}

EventTarget.prototype = {
    constructor: EventTarget,

    addHandler: function(type, handler) {
        if (typeof this.handlers[type] == "undefined") {
            this.handlers[type] = [];
        }
        this.handlers[type].push(handler);
    
    },

    fire: function(event) {
        if (!event.target) {
            event.target = this;
        }
        if (this.handlers[event.type] instanceof Array) {
            var handlers = this.handlers[event.type];
            for (var i = 0; i < handlers.length; i++) {
                handlers[i](event);
            }
        }
    },

    removeHandler: function(type, handler) {
        if (this.handlers[event.type] instanceof Array) {
            var handlers = this.handlers[event.type];
            for (var i = 0; i < handlers.length; i++) {
                if (handlers[i] == handler) {
                    break;
                }    
            }
            handlers.splice(i, 1);
        }
    }
}

var target = new EventTarget();

target.addHandler("message", handleMessage);
target.fire({type: "message", message: "hello world!"});
```






































































