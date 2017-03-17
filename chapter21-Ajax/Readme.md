### Ajax 与 Comet
#### XMLHttpRequest 对象
IE6- 需要用ActiveX对象实现  
IE7+ 支持原生的XHR对象

```
function createXHR() {
    if (typeof XMLHttpRequest != "undefined") {
        return new XMLHttpRequest();
    } else if (typeof ActiveXObject != "undefined") {
        if (typeof arguments.callee.activeXString != "string") {
            var versions =  ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0", "MSXML2.XMLHttp"],
                            i, len;
            for (i = 0; i < versions.length; i++) {
                try {
                    new ActiveXObject(versions[i]);
                    arguments.callee.activeXString = versions[i];
                    break;
                } catch(ex) {
                
                }
            }
        }
        return new ActiveXObject(arguments.callee.activeXString);
    } else {
        throw new Error("no xhr object available.")
    }

}
```

##### XHR 用法
- xhr.open("get", "example.php", false)  启动一个请求，准备发送
- xhr.send(null) 发送请求，参数为发送的数据
- xhr.abort() 在接收到响应之前调用abort() 方法取消异步请求

收到响应后，响应会填充自动填充XHR对象的属性

- responseText 作为响应主体被返回的文本
- responseXML 如果响应的内容类型是"text/xml"或"application/xml"，这个属性将保存包含着响应数据的XML DOM文档
- status 响应的HTTP状态
- statusText HTTP状态的说明

```
if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
   alert(xhr.responseText);
} else {
   alert("request was unsuccessful: " + xhr.status);
}
```

XHR 对象的 readyState 的值

 - 0 未初始化，还没调用open()
 - 1 启动，调用了open()，但是没有调用send()
 - 2 发送，已经调用了send()
 - 3 接收到部分响应数据
 - 4 接收到全部响应数据

```
var xhr = createXHR();
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4) {
        if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
            alert(xhr.responseText);
        } else {
            alert("request was unsuccessful: " + xhr.status);
        }
    }
}
xhr.open('get', "example.txt", true);
xhr.send(null);
```

##### HTTP 头部信息
- Accept 浏览器能够处理的内容类型
- Accept-Charset 浏览器能够显示的字符集
- Accept-Encoding 浏览器能够处理的压缩编码
- Accept-Language 浏览器当前设置的语言
- Connection 浏览器与服务器之间连接的类型
- Cookie 当前页面设置的任何cookie
- Host 发出请求的页面所在的域
- Referer 发出请求的页面的URI
- User-Agent 浏览器的用户代理字符串

setRequestHeader()方法设置自定义头部，需在调用open()方法之后，调用send()方法之前调用。

getResponseHeader()方法取得响应头部信息。  
getAllResponseHeaders()取得所有头部信息。

```
xhr.open('get', 'example.php', true);
xhr.setRequestHeader('myheader', 'myvalue');
xhr.send(null);

var myHeader = xhr.getResponseHeader('myheader');
var allHeaders = xhr.getAllResponseHeaders();
```

##### GET 请求
给URL的末尾添加查询字符串参数

```
function addURLParam(url, name, value) {
    url += url.indexOf("?") == -1 ? "?" : "&";
    url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
    return url;
}
```

##### POST 请求
数据不是追加在URL的末尾，数据作为请求的主体提交，在send()方法中传入数据，数据要进行序列化。

发送相同的数据，get请求比post快。

#### XMLHttpRequest 2级
##### FormData
序列化表单

```
var data = new FormData();
data.append("name", "nicholas");

// 表单
var data = new FormData(document.forms[0]); 
```
##### 超时设定timeout
timeout，如果在规定的时间内浏览器还没有接收到响应，会终止请求，触发timeout事件。

```
xhr.open("get", "timeout.php", true);
xhr.timeout = 1000;
xhr.ontimeout = function() {
    alert("timeout!")
}
xhr.send(null);
```
##### overrideMimeType()
让XHR对象将响应当作某类型来处理。

```
xhr.open("get", "text.php", true);
xhr.overrideMimeType("text/xml");
xhr.send(null);
```

#### 进度事件
- loadstart 在接收到响应数据的第一个字节时触发
- progress 在接收响应期间持续不断地触发
- error 在请求发生错误时触发
- abort 在因为调用abort()方法而终止链接时触发
- load 在接收到完整响应数据时触发
- loadend 在通信完成或者触发error，abort或load事件后触发

```
xhr.onprogress = function(event) {
    if (event.lengthComputable) {
        console.log(event.position + "of" + event.totalSize + "bytes");
    }
}

xhr.open("get", "altevents.php", true);
xhr.send(null);
```

#### 跨源资源共享 (Crossing-Origin Resource Share)
同源策略是对XHR的一个主要约束，它为通信设置了“相同的域、相同的端口、相同的协议”这一限制。试图访问限制外的资源，都会引发安全错误。  
跨域资源共享就是跨域解决方案。  
主要检查Origin 和 Access-Control-Allow-Origin 是否匹配。  

##### IE对CORS的实现
有一个xdr对象：

- 请求不带cookie
- 只能设置请求头部信息中的Content-Type字段
- 不能访问响应头部信息
- 只支持GET和POST请求

```
var xdr = new XDomainRequest();
xhr.onload = function() {
    alert(xdr.responseText);
};
xhr.onerror = function() {
    alert("An error occurred!");
}
xdr.open("post", "http://www.wrox.com");
xdr.contentType = "applicatiob/x-www-form-urlencodeed";
xdr.send("name=value");
```

其他浏览器的XMLHttpRequest对象实现了对CORS的原生支持。  
不能使用setRequestHeader()设置自定义头部  
不能发送和接收cookie  
getAllResponseHeaders()返回空字符串  

##### Prefighted Requests
在特别的情况（设置了自定义头部，使用了GET,POST之外的方法）时，在正式发请求前，浏览器会发送一个Prefight请求，为OPTION方法，发送下列头部

- Origin
- Access-Control-Request-Method 请求自身使用的方法
- Access-Control-Request-Headers 自定义头部信息

服务器通过在响应中发送如下头部与浏览器进行沟通。  

- Access-Control-Allow-Origin
- Access-Control-Allow-Methods 允许的方法
- Access-Control-Allow-Headers 允许的头部
- Access-Control-Max-Age 应该将这个Preflight请求缓存多长时间

##### 带凭据的请求
默认不带凭据（cookie，HTTP认证及客户端SSL证明），可以设置withCredentials来发送凭据。  
如果服务端接受凭据，则会通过Access-Control-Allow-Credentials来响应。

```
xhr.withCredentials = true;
```

#### 其他跨域技术
##### 图像Ping
```
var img = new Image();
img.onload = img.onerror = function() {
    alert("Done!");
}
img.src = "http://www.wrox.com";
```
只能发送GET请求，无法访问服务器的响应文本，只能用于浏览器与服务器的单向通信。
##### JSONP
由两部分组成：回调函数和数据。

```
http://free.net/json/?callback=handleResponse
```

JSONP通过动态`<script>`元素来使用的，使用时指定一个跨域的URL。在请求完成后，会立即执行回调函数。

```
function handleResponse(response) {
    console.log(response.ip + "," + response.city);
}

var script = document.createElement("script");
script.src = "http://free.net/json?callback=handleResponse";
document.body.insertBefore(script, document.body.firstChild);
```

可以访问响应文本，支持浏览器与服务器的双向通信。无法确定是否失败。

##### Comet
Ajax是一种从页面向服务器请求数据的技术，而Comet则是一种服务器向页面推送数据的技术。

两种方式实现Comet的方式：长轮询和流。

长轮询  
浏览器定时向服务器发送请求，看有没有更新的数据

* 短轮询  

```
浏览器------------------------------------
         \      /        \      /
     请求  \   / 响应       \   /
            \/              \/
服务器------------------------------------
```

* 长轮询  
页面发送一个到服务器的请求，然后服务器一直保持连接打开，知道有数据可发送。发送完毕后，浏览器关闭连接，随即又发起一个到服务器的新请求。

```
浏览器-------------------------------------
         \             /      \          /
     请求  \          / 响应     \       /
            \       /            \    /
服务器-------------------------------------
```

* HTTP流  
浏览器向服务器发送一个请求，而服务器保持连接打开，然后周期性地向浏览器发送数据。  

```
function createStreamClient(url, progress, finished) {
    var xhr = new XMLHttpRequest(), received = 0;

    xhr.open("get", url, true);
    xhr.onreadystatechange = function() {
        var result;
        if (xhr.readyState == 3) {
            result = xhr.responseText.substring(received);
            received += result.length;
            progress(result);
        } else if (xhr.readyState == 4) {
            finished(xhr.responseText);
        }
    };
    
    xhr.send(null);
    return xhr;
}
```

##### 服务器发送事件(Server-Sent Events)
SSE API用于创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据。服务器响应的MIME类型必须是text/event-stream。  
SSE 适合长轮询和HTTP流。
要预定的新的事件流，首先要创建一个新的EventSource对象。EventSource的实例有一个readyState属性，值0表示正连接到服务器，值为1表示打开来连接，值为2表示关闭了连接。  
有三个事件open，message，error  

```
var source = new EventSource("myevents.php");

source.onmessage = function(event) {
    var data = event.data;
}

// 强制断开连接
source.close();
```

##### Web Sockets
Web Sockets的目标是在一个单独的持久连接上提供全双工、双向通信。  
创建Web Socket之后，会有一个HTTP请求发送到浏览器以发起连接，在取得服务器响应后，建立的连接会使用HTTP升级从HTTP协议交换为Web Socket协议。  
Web Sockets自定义协议。ws://，加密的连接 wss://。传递的数据少。

* Web Socket API 

```
var socket = new WebSocket("ws://www.example.com/server.php");
socket.send("hello world!");

socket.onmessage = function(e) {
    var data = e.data;
}

// 在成功建立连接时触发
socket.onopen = function() {
    alert("connection established.")
};

socket.onerror = function() {
    alert("connection error.")
};

socket.onclose = function() {
    alert("connection closed.")
}

// 关闭连接
socket.close()
```

WebSocket也有一个表示当前状态的readyState属性

- WebSocket.OPENING(0) 正在建立连接
- WebSocket.OPEN(1) 已经建立连接
- WebSocket.CLOSING(2) 正在关闭连接
- WebSocket.CLOSE(3) 已经关闭连接

#### 安全
对于未被授权的系统有权访问某个资源的情况，我们称之为CSRF（Cross-Site Request Forgery，跨站点请求伪造）。  
为确保通过XHR访问的URL安全，通行的做法就是验证发送请求者是否有权限访问相应的资源。  

- 要求以SSL连接来访问可以通过XHR请求的资源
- 要求每一次请求都要附带经过相应算法计算得到的验证码。
