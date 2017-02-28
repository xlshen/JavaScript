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
1. type(字符串): 表示触发的类型,如“click”    
2. bubbles(布尔型): 表示是否应该冒泡。为了精确模拟鼠标事件，应该设置为true    
3. cancelable(布尔型): 表示事件是否可以取消。为了精确模拟鼠标事件，应该设置为true    
4. view(AbstractView): 与事件关联的视图。这个参数几乎总是document.defaultView    
5. detail(整数): 与事件详细信息。这个一般只有时间处理程序使用。通常为0    
6. screenX(整数): 事件相对于屏幕X坐标    
7. screenY(整数): 事件相对于屏幕Y坐标    
8. clientX(整数): 事件相对于视口X坐标    
9. clientY(整数): 事件相对于视口Y坐标    
10. ctrlKey(布尔值): 表示是否按下了Ctrl键。默认false    
11. altKey(布尔值): 表示是否按下了Alt键。默认false    
12. shiftKey(布尔值): 表示是否按下了Shift键。默认false   
13. metaKey(布尔值): 表示是否按下了Meta键。默认false   
14. button(整数): 表示按下了哪一个鼠标键。默认0    
15. relatedTarget(对象): 表示与事件相关的对象。这个参数只在mouseover,mouseout时使用
