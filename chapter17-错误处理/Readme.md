### 错误处理与调试
#### 错误处理
##### try-catch 语句  
```
try {
    return 2;
} catch (error) {
    alert(error.message);
    return 1;
} finally {
    return 0; // 无论如何都会执行
}
```

错误类型

- Error
- EvalError
- RangeError
- RefernceError
- SyntaxError
- TypeError
- URIError

适合处理我们无法控制的错误。

##### 抛出错误  
使用throw抛出错误，代码会立即停止执行。仅当try-catch语句捕获到被抛出的值时，代码才会继续执行。  

```
throw new Error("something had happened!");
throw new TypeError("what type of variable do you take me for?")
```

自定义错误类型，指定name和message
捕获错误的目的在于避免浏览器以默认方式处理它们；而抛出错误的目的在于提供错误发生具体原因的消息。  

##### 错误事件
没有被捕获的错误会触发浏览器的window对象的error事件。  
事件接收的参数：错误消息、错误所在的URL和行号。  

```
window.onerror = function(message, url, line) {
    alert(message);
    return false;  // 阻止浏览器报告错误的默认行为
}
```

##### 常见的错误类型
- 类型转换错误
- 数据类型错误
- 通信错误

类型转换错误

```
function concat(str1, str2, str3) {
    var res = str1 + str2;
    if (str3) {   // bad, 转换为boolean值，但是传入的变量未知，不一定是字符串
        res += str3;
    }
    if (typeof str3 == "string") {
        res += str3;
    }
    return res;
}
```

数据类型错误，使用某类型的方法(例如sort, concat)前，需要去检测数据的类型。

##### 把错误记录到服务器
通常把这些错误写入到保存服务器端错误的地方，只不过标明它们来自前端。

```
function logError(sev, msg) {
    var img = new Image();
    img.src = "log.php?sev=" + encodeURIComponent(sev) + "&msg=" + encodeURIComponent(msg);
}
```
 
##### 将信息记录到控制台
console对象的方法：

- error(msg) // 将错误消息记录到控制台
- info(msg) // 将信息性消息记录到控制台
- log(msg) // 将一般消息记录到控制台
- warn(msg) // 将警告消息记录到控制台 

##### 将消息记录到当前页面

```
function log(msg) {
    var console = document.getElementById('debuginfo');
    if (console === null) {
        console = document.createElement("div");
        console.id = "debuginfo";
        console.style.background = "#dedede";
        console.style.border = "1px solid silver";
        console.style.padding = "5px";
        console.style.width = "400px";
        console.style.position = "absolute";
        console.style.right = "0";
        console.style.top = "0";
        document.body.appendChild(console);
    }
    console.innerHTML += "<p>" + msg + "</p>";
}
```

##### 抛出错误
```
function assert(condition, msg) {
    if (!condition) {
        throw new Error(msg);
    }
}
```


