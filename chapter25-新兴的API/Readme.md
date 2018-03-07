### 新兴的API

1. requestAnimationFrame() 是一个着眼于优化JavaScript动画的API，能够在动画运行期间发出信号。
2. Page Visibility API 页面可见性
3. Geolocation API 确定用户的位置

#### File API 

- Files对象
- FileReader 类型
异步文件读取机制。读取的是文件系统
    - readAsText(file, encoding)
    - readAsDataURL(file) 读取文件并将数据URI的形式保存在result属性
    - readAsBinaryString(file) 读取文件并将一个字符串保存在result属性中
    - readAsArrayBuffer(file) 读取文件并将一个包含文件内容的ArrayBuffer保存在result属性中。
读取部分文件 slice()  
读取拖放的文件 event.dataTransfer.files  
上传文件 // (new FormData()).append("file"+ i, file[i])
- 文件成功加载后会触发load事件
- 中断读取过程，abort()

#### Web 计时
核心是window.performance对象。performance.timing记录不同事件产生的不同的时间值。  

#### Web Workers
异步执行代码，避免阻塞用户界面。  
它所执行的代码完全在另一个作用域中。不能访问window    
全局对象是worker本身。

创建：
```
  var worker = new Worker(a.js) // 下载a.js，接收到消息再执行
  worker.postMessage('hi, i am window') // 给worker传递消息
  worker.onMessage = function (e) {  // 接收worker发来的消息
    console.log(e.data);
  }

```
worker 与 页面通信
```
// a.js
  self.onmessage = function(e) { // 接收来自页面的消息
    // ...
  }
  self.postMessage('hi, i am worker') // 发送消息给页面
```

终止
外部`worker.terminate()` or 内部`self.close()`


