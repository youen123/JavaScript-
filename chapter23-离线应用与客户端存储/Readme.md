### 离线应用与客户端存储
#### 离线检测
HTML5为此定义了一个navigator.onLine属性，两个事件onLine 和 offLine。

#### 应用缓存appcache
从浏览器的缓存中分出一块缓存区，可以用一个`描述文件`列出下载和缓存的资源。  
描述文件与页面关联

```
<html manifest="/offline.mainfest">
```
* applicationCache对象
    - status
        - 0 无缓存
        - 1 闲置，应用缓存未得到更新
        - 2 检查中，即正在下载描述文件并检查更新
        - 3 下载中
        - 4 更新完成
        - 5 废弃，应用缓存的描述文件已经不在了
    - 事件
        - checking 查找更新
        - error 检查更新或下载资源时发生错误
        - noupdate 检查描述文件发现文件无变化时触发
        - downloading 在开始下载应用缓存资源时触发
        - progress 文件下载应用缓存时持续不断地触发
        - updateready 在页面新的应用缓存下载完毕且可以通过swapCache()使用时触发
        - cached 应用缓存完整可用时触发
    - 方法
        - update 

#### 数据存储
##### cookie 
最初是在客户端用于存储会话信息的。要求服务器的响应的头要带Set-Cookie，而之后的请求都会有Cookie这个头。

* 限制

 - 个数限制，不同浏览器限制个数不同20～50+
 - 大小限制，4KB
 
* cookie的构成
 
 - 名称：不区分大小写，必须经过URL编码
 - 值：URL编码
 - 域（domain）：cookie的有效域
 - 路径(path)：那个路径需要发送cookie
 - 失效时间（expires）
 - 安全标志(secure)：是否是在SSL连接时才发送到服务器

```
Set-Cookie: name=value; domain=.wrox.com; path=/; secure
```
##### JavaScript 中的 cookie
`document.cookie`获取或设置当前页面的cookie

```
var CookieUtil = {
    get: function(name) {
        var cookieName = encodeURIComponent(name) + "=",
            cookieStart = document.cookie.indexOf(cookieName),
            cookieValue = null;

        if (cookieStart > -1) {
            var cookieEnd = document.cookie.indexOf(";", cookieStart);
            if (cookieEnd == -1) {
                cookieEnd = document.cookie.length;
            }
            cookieValue = decodeURIComponent(document.cookie.subtring(cookieStart + cookieName.length, cookieEnd));
        }
        return cookieValue;
    }

    set: function(name, value, expires, path, domain, secure) {
        var cookieText = encodeURIComponent(name) + "=" + encodeURIComponent(value);

        if (expires instanceof Date) {
            cookieText += "; expires=" + expires.toGMTString();
        }
        
        if (path) {
            cookieText += "; path=" + path;
        }
        if (domain) {
            domain += "; domain=" + domain;
        }
        if (secure) {
            secure += "; secure";
        }

        document.cookie = cookieText;
    },

    unset: function(name, path, domain, secure) {
        this.set(name, "", new Date(0), path, domain, secure);
    }

}
```

##### 子cookie
子cookie是存在单个cookie中的更小段的数据。

##### IE用户数据
在元素上指定`userData`行为，可以用`setAttribute()`保存数据，用`save()`告诉它要保存到数据控件的名字。用`load()`方法指定同样的数据空间名称来获取数据。用`removeAttribute()`删除数据。

```
<div style="behavior:url(#default#userData)" id="dataStore"></div>
...

var dataStore = document.getElementById("dataStore");
dataStore.setAttribute("name", "nicholas");
dataStore.save("BookInfo");
...
dataStore.load("BookInfo");
dataStore.getAttribute("name");

dataStore.removeAttribute("name");
dataStore.save("BookInfo");
```

##### Web 存储机制
###### Storage 类型  
存储键值对。它的方法

 - clear() 删除所有值
 - getItem(name) 
 - key(index) 获得index 位置处的值的名字
 - removeItem(name)
 - setItem(name, value)

###### sessionStorage 对象  
存储特定于某个会话的数据，浏览器关闭后数据清除。存储sessionStorage中的数据只能由最初给对象存储数据的页面访问到，所以对多页面应用有限制。  
设置数据

```
sessionStorage.setItem("name", "gulu");

sessionStorage.book = "Professional JavaScript";
```

写入磁盘的策略不同浏览器不同，有的同步，有的异步。

###### globalStorage 对象  
跨会话存储，但是对域有要求。存储空间对某个域及其子域都是可以访问的。

```
globalStorage["www.wrox.com"].name = "nicholas";
globalStorage["www.wrox.com"].getItem("name");
```

###### localStorage 对象  
持久保存客户端方案。要访问同一个localStorage对象，页面必须来自同一个域名，使用同一中协议，在同一个端口上。

```
localStorage.setItem("name", "nicholas");
localStorage.getItem("name");

var name = localStorage.name;
```

###### storage 事件  
对Storage对象进行任何修改，都会在文档上触发storage事件。  
事件的属性：

- domain 发生变化的存储空间的域名
- key 设置或删除的键名
- newValue 如果是删除，则是null
- oldValue

###### 限制
localStorage 2.5MB 或 5MB
sessionStorage 2.5MB 或 5MB

##### IndexedDB
是在浏览器保存结构化数据的一种数据库。  
操作完全是异步的。IndexedDB是一个作为API宿主的全局对象。

###### 数据库
它利用对象保存数据，而不是表。一个IndexedDB数据库，就是一组位于相同命名空间下的对象的集合。

```
var request, database;

request = indexedDB.open("admin"); // 返回一个IDBRequest对象
request.onerror = function(e) {
    console.log(e.errorCode);
}

request.onsuccess = function(e) {
    database = e.target.result; // 返回数据库实例对象（IDBDatabase）
}
```

用`setVersion()`来指定版本号

###### 对象存储空间
`add()`添加新值，`put()`更新原有的值

```
var store = db.createObjectStore("users", { keyPath: "username" });
var user = {
    username: "007",
    name: "jerry"
}

var request = store.add(user);
request.onerror = function() {
    // ...
}
request.onsuccess = function() {
    // ...
}
```

###### 事务
对存储进行读取或修改
`transaction()`读取数据库中保存的对象，`objectStore()`访问特定的存储空间

```
var request = db.transaction("users").objectStore("users").get("007");
// ...
```

###### 使用游标查询
`openCursor()`方法创建游标，在`onsuccess`事件中`event.target.result`为`IDBCursor`实例。  
`IDBCursor`实例的属性：

- direction 游标移动的方向
- key 对象的键
- value 实际的对象
- primaryKey 游标使用的键

`IDBCursor`实例的方法

- update() 方法可以用指定的对象更新当前游标的value。
- delete() 会删除相应的记录
- continue(key) 移动游标，会触发另一次请求，再次调用onsuccess事件处理程序。
- advance(count) 向前移动count指定的项数。

```
var store = db.transaction("users").objectStore("users"),
    request = store.openCursor();

request.onerror = function() {
    // ...
};
request.onsuccess = function(e) {
    var cursor = e.target.result; // 一个IDBCursor的实例
    if (cursor) {
        console.log("Key: " + cursor.key + ", Value: " + JSON.stringfy(cursor.value));
    }
    var value = cursor.value;
    value.password = "magic";
    cursor.update(value);
}
```

###### 键范围
IDBKeyRange的实例表示。

- only(key) 取到键为key的对象
- lowerBound() 下界
- upperBound() 上界
- bound() 同时指定上下界

```
var store = db.transaction("users").objectStore("users"),
    range = IDBKeyRange.bound("007", "ace"),
    request = store.openCursor(range);
// ...
```

###### 设定游标方向
`openCursor()`可以接收两个参数，第一个参数就是`IDBKeyRange`的实例，第二个是表示方向的数值常量（IDBCursor.NEXT_NO_DUPLICATE等）。

###### 索引
一个对象需要多个键，对主键之外的键，可以用`createIndex()`为其创建索引。  
索引的属性：

- name 索引的名字
- keyPath 传入的createIndex() 中的属性路径
- objectStore 索引的对象存储空间
- unique 索引键是否唯一

索引的方法：

- createIndex() 创建索引
- deleteIndex() 删除索引
存储对象的`indexName`属性可以访问到该空间所有索引。

```
var store = db.transaction("users").objectStore("users"),
// 创建索引
index = store.createIndex("username", "username", { unique: false });
// ...
index = store.index("username");
// 创建游标
var request = index.openCursor(); 
// var request = index.openKeyCursor();

request.onsuccess = function(e) {
    // 处理成功
    // event.target.result.key 中保存索引键，event.target.result.value 保存主键
}

// 从索引中取得一个对象
var request = index.get("007")
```

###### 并发问题
两个不同的标签页打开了同一个页面，一个页面试图更新另一个页面的数据。打开数据库时，要指定`onversionchange`事件处理程序。当另一个页面调用`setVersion`时，就会执行这个回调函数。处理这个事件的最佳方式是立即关闭数据库。  
在你想要更新数据库版本但另一个标签页已经打开数据库的情况下，就会触发onblocked事件。

```
var request, database;

request = indexedDB.open("admin");

request.onblocked = function() {
    alert("Please close all other tabs");
}
request.onsuccess = function(event) {
    database = event.target.result;

    database.onversionchange = function() {
        database.close();
    }
}
```

###### 限制
只能同源页面操作，大小受限，5MB-50MB。



















































