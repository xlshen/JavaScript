#### 原生拖放
##### 拖放事件
有些事件在拖放元素上触发，有些事件是在放置目标上触发。
> 1. dragstart
> 2. drag
> 3. dragend

按下鼠标拖动触发`dragstart`事件，通过`ondragstart`事件处理，触发dragstart后，即会触发`drag`事件，而且在拖动过程中会持续触发。该事件和`mouseover`事件类似，停止拖动触发`dragend`事件。  
当某个元素被拖动到一个有效的目标上，会触发：
> 1. dragenter
> 2. dragover
> 3. dragleave或drop

只要元素被拖动到目标上时就会触发dragenter事件，然后dragover事件，在目标元素上移动时持续触发。当元素拖出目标外就会触发dragleave事件。如果元素被放置到目标中，触发drop事件而不是dragleave事件。上述事件都是在目标元素上触发。
##### 自定义放置目标
虽然所有元素都支持放置目标事件，但这些元素默认是不允许放置的。但我们可以把任何元素变成可放置的目标，方法为重写dragenter和dragover事件的默认行为：
```javascript
var dropstart = document.getElementById("droptarget");
Event.addHandle(dragtarget, "dragover", function(event){
  Event.preventDefault(event);
});
Event.addHandle(dragtarget, "dragenter", function(event){
  Event.preventDefault(event);
});
// 兼容FF3.5+，也要取消drop默认行为
Event.addHandle(dragtarget, "drop", function(event){
  Event.preventDefault(event);
});
```
##### dataTransfer对象
事件对象的属性，用于从被拖动的元素向放置目标传递字符串格式的数据，只能在拖动事件处理程序中访问该属性。
>1. getData(kk)
> 2. setData(kk, value) // kk是一个字符串，取值“text”等各种MIME值

```javascript
// 设置和接收文本
event.dataTransfer.setData("text", "Sth");
var text = event.dataTransfer.getData("text");
```
保存的`tranTransfer`对象只有在`drop`时间处理程序中读取，如果`drop`事件也没有取到，说明`dataTransfer`对象已经被销毁，数据丢失
##### dropEffect和effectAllowed
通过`dataTransfer`对象的`dropEffect`和`effectAllowed`属性可以确定被拖元素和作为放置目标元素能够接收什么操作
`dropEffect`: 确定被拖放元素能执行哪些放置行为
> 1. none: 不能把拖放元素放到这。除了文本框之外所有元素的默认值
> 2. move: 应该把拖放元素移动到放置目标
> 3. copy: 应该把拖放元素复制到放置目标
> 4. link: 防止目标会打开拖放的元素(拖动元素必须是一个链接)

`要使用dropEffect属性，必须在ondragenter事件处理程序中设置`  
dropEffect属性只有配合effectAllowed属性才有用。effectAllowed属性表示允许拖动元素的哪种dropEffect，值如下：
> 1. uninitialized: 没有给拖动元素设置任何放置行为
> 2. none: 被拖动的元素不能有任何行为
> 3. copy: 只允许值为"copy"的dropEffect
> 4. link: 只允许值为"link"的dropEffect
> 5. move: 只允许值为"move"的dropEffect
> 6. copyLink: 允许值为"copy"和"link"的dropEffect
> 7. copyMove: 允许值为"copy"和"move"的dropEffect
> 8. linkMove: 允许值为"link"和"move"的dropEffect
> 9. all: 允许任意的dropEffect

`要使用effectAllowed属性，必须在ondragstart事件处理程序中设置`
##### 可拖动
默认，`图像`、`链接`和`文本`是可拖动的。
HTML5提供了一个`draggable`属性，表示元素是否可以拖动，图像和链接自动设置为true，其他默认false
```html
<img src="default.png" draggable="false" alt="default"/>
```
`[用拖拽实现不完美拖动弹框]`
```html
<style>
    #mask{
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        height: 100%;
        width: 100%;
        background-color: rgba(0, 0, 0, .4);
    }
    #dragTest{
        position: fixed;
        width: 300px;
        top: 50%;
        left: 50%;
        -webkit-transform: translate(-50%,-50%);
        -moz-transform: translate(-50%,-50%);
        transform:translate(-50%,-50%);
        background-color: #fff;
    }
    header{
        font-size: 20px;
        font-weight: bold;
        text-align: center;
        cursor: move;
        color: #666;
        border-bottom: 1px solid #ddd;
        line-height: 2;
    }
    .dragContent{
        text-align: center;
    }
</style>
<div id="mask"></div>
<div id="dragTest" draggable="true">
    <header>测试头</header>
    <div class="dragContent">
        <h3>drag测试案例</h3>
    </div>
</div>
<script>
    var leftNum, topNum;
    document.getElementById("dragTest").addEventListener("dragstart", function(event){
        // 需要增加document的dragover事件，否则无法准确获取clientX和clientY属性
        document.addEventListener("dragover", function(event){ 
            topNum = event.clientY;
            leftNum = event.clientX;
            console.log(event.clientX, event.clientY);
        }, false);
    }, false);
    document.getElementById("dragTest").addEventListener("drag", function(event){
        document.getElementById("dragTest").style.top = topNum + "px";
        document.getElementById("dragTest").style.left = leftNum + "px";
    }, false);
</script>
```
#### 媒体元素
`<audio>`与`<video>`
```html
<video src="" controls=true autoplay width="100%" height="200" poster="img.png">
  <source src="">
  不支持HTML5播放器
</video>
<audio src="" controls=true autoplay width="100%" height="200">
  <source src="">
  不支持HTML5播放器
</audio>
```
##### 自定义媒体播放器
使用`<audio>`和`<video>`元素的`play()`和`pause()`方法，可以手动控制播放媒体文件。组合使用属性，事件和这两个方法，可以很容易创建一个自定义的媒体播放器。
```html
<div class="mediaplayer">
  <div class="video">
    <video id="player" src="movie.mov" poster="img.png" width="200" height="200"> 
      不支持
    </video>
  </div>
  <div class="controls">
    <input type="button" value="Play" id="video-btn">
    <span id="curtime">0</span>/<span id="duration">0</span>
  </div>
</div>
<script>
  var player = document.getElementById("player"),
      btn = document.getElementById("video-btn"),
      curtime = document.getElementById("curtime"),
      duration = document.getElementById("duration");
  // 更新播放时间
  duration.innerHTML = player.duration; // 加到load事件里面，如果视频没加载，此值无法获取
  Event.addHandle(btn, "click", function(event){
    if(player.paused){
      player.play();
      btn.value = "Pause";
    }else{
      player.pause();
      btn.value = "Play";
    }
  });
  setInterval(function(){
    curtime.innerHTML = player.currentTime;
  },200);
</script>
```
##### Audio类型
`<Audio>`元素还有一个原生的JavaScript构造函数Audio，可以在任何时候播放音频。从DOM角度看，Audio和Image很相似，但Audio不必像Image那样必须插入到文档中，只要创建一个新实例，并传入音频文件即可。
```javascript
var audio = new Audio("player.mp3");
Event.addHandle(audio, "canplaythrough", function(){
  audio.play();
});
```
##### 历史状态管理
> HTML5新增了`pushState()`和`replaceState()`方法管理历史记录  
1. `pushState`接收三个参数：状态对象、新状态标题和相对URL  
2. `replaceState`接收两个参数：状态对象，新状态标题  
```javascript
history.pushState({
  "first": 1,
  "second": 2
}, "page", "/index.html");
```
执行`pushState()`方法后，新状态被压入状态栈，浏览器地址栏改为新设置的地址，但不会去请求服务器。第二个参数现无浏览器实现，可以传空字符串。  
`pushState()`方法创建了新历史状态，浏览器会出现“后退”操作，点击后退或者JavaScript执行后退操作，触发`popstate`事件，该事件有个`state`属性，该属性保存着`pushState()`方法传入的第一个参数对象。
```javascript
Event.addHandle(window, "popstate", function(event){
  var state = event.state;
  if(state){ // 回撤到第一个页面state属性为null！
    // 
  }
});
```
执行`replaceState()`方法会直接替换掉当前URL，但是不会在历史栈中创建新状态
```javascript
window.onpopstate = function(event) {
  alert("location: " + document.location + ", state: " + JSON.stringify(event.state));
};
history.pushState({page: 1}, "title 1", "?page=1"); // 压入历史栈，替换URL
history.pushState({page: 2}, "title 2", "?page=2"); // 压入历史栈，替换URL
history.replaceState({page: 3}, "title 3", "?page=3"); // 替换URL，不压历史栈，这个操作相当于把上一操作pushState操作抹掉了
history.back(); // alerts "location: http://example.com/example.html?page=1, state: {"page":1}" // 因为replaceState原因，历史栈上一状态为第一次操作内容
history.back(); // alerts "location: http://example.com/example.html, state: null // 回到最初状态，所以state为null
history.go(2);  // alerts "location: http://example.com/example.html?page=3, state: {"page":3} // 回到replaceState操作时状态
```
