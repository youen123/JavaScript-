### HTML5脚本编程

### 跨文档消息传送（cross-document messaging）XDM
一个页面向它的iframe元素或者当前页面弹出的窗口传送信息。  
核心： `postMessage()`  
参数

- 消息
- 消息接收方来自哪个域  

```
var iframeWindow = document.getElementById('myframe').contentWindow;
iframeWindow.postMessage("A secret", "http://www.wrox.com");
```

收到消息时，会触发window对象的message事件。包括以下重要信息

- data 消息的字符串数据
- origin  发送方的文档所在的域
- source 发送消息的文档的window对象的代理。主要用于调用postMessage()方法。

#### 原生拖放
##### 拖放事件
拖动某元素  

- dragstart
- drag
- dragend 

拖动到一个有效的放置目标  

- dragenter
- dragover
- dragleave 或 drop

##### 自定义放置的目标
把元素变成有效的目标，方法是重写dragenter 和 dragover事件的默认行为。 FF需要取消drop事件。   

##### dataTransfer对象
事件对象的属性，用于从被拖动元素向放置目标传递字符串格式的数据。  
两个方法`getData()`和`setData()`

```
// 设置和接收文本数据
event.dataTransfer.setData("text", "some text");
var text = event.dataTransfer.getData("text");

// 设置和接收URL
event.dataTransfer.setData("URL", "http://www.wrox.com/");
var url = event.dataTransfer.getData("URL");
```

##### dropEffect 与 effectAllowed
dropEffect属性，可以知道被拖动的元素能够执行哪种放置行为。  
要使用dropEffect属性，必须搭配ondragenter事件处理程序

- "none" 不能把拖动的元素放在这里
- "move" 应该把拖动的元素移动到放置目标
- "copy" 应该把拖动的元素复制到放置目标
- "link" 表示放置目标会打开拖动的元素

effectAllowed属性，在effectAllowed事件处理程序中设置。  

- "unintialized" // 没有给被拖动的元素设置任何放置行为 
- "none" // 被拖动的元素不能有任何行为
- "copy" // 只允许值为"copy"的dropEffect
- "link" // 只允许值为"link"的dropEffect
- "move" // 只允许值为"move"的dropEffect
- "copyLink" // 允许值为"copy"和"link"的dropEffect 
- "copyMove" // 允许值为"copy"和"move"的dropEffect
- "linkMove" // 允许值为"link"和"move"的dropEffect
- "all" // 允许任意的dropEffect

draggable 设置元素是否可以拖动

##### dataTransfer 的其他属性
- addElement(element) 为拖动操作添加一个元素
- clearData(format) 清除以特定格式保存的数据
- setDragImage(element, x, y) 指定一幅图像，当拖动发生时，显示在光标下方。
- types 当前保存的数据类型。

#### 媒体元素
HTML5新增两个标签<audio>和<video>。  

```
<!-- 嵌入视频 -->
<video id="myvideo" poster="" controls>
    <source src="conference.webm" type="video/webm; codecs='vp8, vorbis'">
    <source src="conference.ogv" type="video/ogg; codecs='theora, vorbis'">
    Video player not available.
</video>

<!-- 嵌入音频 -->
<audio src="song.mp3" id="myAudio">Audio player not available.</audio>
```

##### 属性
- autoplay 取得或设置autoplay标志
- buffered 已下载的缓冲的时间范围的对象
- controls 显示或隐藏浏览器内置的控件
- duration 总播放事件
- loop 是否循环
- readyState 是否就绪
- start 取得或设置媒体文件中开始播放的位置
- volume 音量
- ...

##### 事件
- abort 下载终端
- canplay 可以播放
- emptied 网络链接关闭
- play 开始播放
- ended 播放停止
- pause 播放暂停
- progress 正在下载
- ratechange 播放媒体的速度改变
- waiting 播放暂停，等待下载更多数据
- ...

##### 自定义媒体播放器
```
<div class="mediaplayer">
    <div class="video">
        <video id="player" src="movie.mov" poster="mymovie.jpg" width="300" height="200"> 
         video player not available.
        </video>
    </div>
    <div class="controls">
        <input type="button" value="Play" id="video-btn">
        <span id="curtime">0</span>/<span id="duration">0</span>
    </div>
</div>
```
然后用JS来控制播放和暂停。

##### 检测编解码器的支持情况
canPlayType()方法，接收一种格式／编解码器字符串，返回“probably”，“maybe”，“”  

```
if (audio.canPlayType("audio/mpeg")) {
    // ...
}
```
常见的音频格式  

```
MP3 | audio/mpeg   
WAV | audio/mav; codecs="1"  
H.264 | video/mp4; codecs="avc1.42E01E, mp4a.40.2"
```

##### Audio类型
<audio>元素有一个构造函数Audio，但是不用插入到文档中。

```
var audio = new Audio("sound.mp3");
audio.addEventListner('canplaythrough', function(event) {
    audio.play();
})
```

#### 历史状态管理
通过hashchange事件，可以知道URL的参数发生了变化，可以根据状态管理API对历史状态进行管理。  

- pushState({name: 'nocholas'}, "nicholoa's page", "nicholas.html") // 加入历史状态栈，url更新，location.href更新
- replaceState()  更新当前状态

回退会触发window的popstate事件。返回pushState传入的对象。浏览器加载的第一个页面没有状态，回退到第一页时，event.state值为null。
































