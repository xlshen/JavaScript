#### 事件委托
```html
  <script>
    var list = document.getElementById("list");
    Event.addHandle(list, "click", function(event){
      event = Event.getEvent(event);
      var target = Event.getTarget(event);
      switch(target.id){
        case "doSomething":
          document.title = "I changed the doc title.";
          break;
        case "goSomething":
          location.href = "XXX";
          break;
      }
    });
  </script>
```
#### 模拟事件
##### 模拟鼠标事件
> 创建鼠标事件对象的方法createEvent()传入字符串“MouseEvents”。返回对象有一个为initMouseEvent()方法，用于指定与鼠标事件有关的信息，这个方法接收15个参数:  
type(字符串): 表示触发的类型,如“click”    
bubbles(布尔型): 表示是否应该冒泡。为了精确模拟鼠标事件，应该设置为true    
cancelable(布尔型): 表示事件是否可以取消。为了精确模拟鼠标事件，应该设置为true    
view(AbstractView): 与事件关联的视图。这个参数几乎总是document.defaultView    
detail(整数): 与事件详细信息。这个一般只有时间处理程序使用。通常为0    
screenX(整数): 事件相对于屏幕X坐标    
screenY(整数): 事件相对于屏幕Y坐标    
clientX(整数): 事件相对于视口X坐标    
clientY(整数): 事件相对于视口Y坐标    
