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

#### Web 计时
核心是window.performance对象。performance.timing记录不同事件产生的不同的时间值。  

#### Web Workers
异步执行代码，避免阻塞用户界面。它所执行的代码完全在另一个作用域中。全局对象是worker本身。

