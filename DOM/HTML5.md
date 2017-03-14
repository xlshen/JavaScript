#### 原生拖放
##### 拖放事件
有些事件在拖放元素上触发，有些事件是在放置目标上触发。
> 1. dragstart
2. drag
3. dragend

按下鼠标拖动触发`dragstart`事件，通过`ondragstart`事件处理，触发dragstart后，即会触发`drag`事件，而且在拖动过程中会持续触发。该事件和`mouseover`事件类似，停止拖动触发`dragend`事件。  
当某个元素被拖动到一个有效的目标上，会触发：
> 1. dragenter
2. dragover
3. dragleave或drop

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
2. setData(kk, value) // kk是一个字符串，取值“text”等各种MIME值

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
2. move: 应该把拖放元素移动到放置目标
3. copy: 应该把拖放元素复制到放置目标
4. link: 防止目标会打开拖放的元素(拖动元素必须是一个链接)

`要使用dropEffect属性，必须在ondragenter事件处理程序中设置`  
dropEffect属性只有配合effectAllowed属性才有用。effectAllowed属性表示允许拖动元素的哪种dropEffect，值如下：
> 1. uninitialized: 没有给拖动元素设置任何放置行为
2. none: 被拖动的元素不能有任何行为
3. copy: 只允许值为"copy"的dropEffect
4. link: 只允许值为"link"的dropEffect
5. move: 只允许值为"move"的dropEffect
6. copyLink: 允许值为"copy"和"link"的dropEffect
7. copyMove: 允许值为"copy"和"move"的dropEffect
8. linkMove: 允许值为"link"和"move"的dropEffect
9. all: 允许任意的dropEffect

`要使用effectAllowed属性，必须在ondragstart事件处理程序中设置`
##### 可拖动
默认，`图像`、`链接`和`文本`是可拖动的。
HTML5提供了一个`draggable`属性，表示元素是否可以拖动，图像和链接自动设置为true，其他默认false
```html
<img src="default.png" draggable="false" alt="default"/>
```
