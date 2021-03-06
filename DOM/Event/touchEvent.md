#### 触摸与手势事件
> touchstart: 当手指触摸屏幕时触发；及时已经有一个手指放在了屏幕上也会触发  
touchmove: 当手指在屏幕上滑动时连续触发。在这个事件发生期间，调用event.preventDefault()可以阻止滚动  
touchend: 当手指从屏幕上移开时触发  
touchcancel: 当系统停止跟踪触摸时触发。

所有事件都会冒泡，除了常见的DOM属性外，触摸事件还包含下列三个用于跟踪触摸的属性：
> touches: 表示当前跟踪触摸操作的Touch对象的数组    
targetTouches: 特定于事件目标的Touch对象的数组    
changedTouches: 表示自上次触摸以来发生了什么变化的Touch对象的数组    
每个Touch对象包含下列属性:  
clientX: 触摸目标在视图中x坐标    
clientY: 触摸目标在视图中y坐标  
identifier: 触摸唯一的ID  
pageX: 触摸目标在页面x坐标  
pageY: 触摸目标在页面y坐标  
screenX: 触摸目标在屏幕中的x坐标  
screenY: 触摸目标在屏幕中的y坐标  
target: 触摸DOM节点目标  
```html
<script>
  function handleTouchEvent(event){
    // 只跟踪一次触摸
    if(event.touches.length === 1){
      var output = document.getElementById("output");
      switch(event.type){
        case "touchstart": 
          output.innerHTML = "Touch started ( " + event.touches[0].clientX + ", " + event.touches[0].clientY + " .)";
          break;
        // 此处有误！！！touchend事件不会触发，如果触发touches数组为空，添加判断
        case "touchend":
          output.innerHTML = "Touch ended ( " + event.changedTouches[0].clientX + ", " + event.changedTouches[0].clientY + " .)";
          break;
        case "touchmove":
          event.preventDefault(); 
          output.innerHTML = "Touch moved ( " + event.changedTouches[0].clientX + ", " + event.changedTouches[0].clientY + " .)";
          break;
      }
    }else if(event.touches.length === 0){
      var output = document.getElementById("output");
      switch(event.type){
        // 此处判断
        case "touchend":
          output.innerHTML = "Touch ended ( " + event.changedTouches[0].clientX + ", " + event.changedTouches[0].clientY + " .)";
          break;
        case "touchcancel":
          output.innerHTML = "Touch ended ( " + event.changedTouches[0].clientX + ", " + event.changedTouches[0].clientY + " .)";
          break;
      }
    }
  }
  Event.addHandle(document, "touchstart", handleTouchEvent);
  Event.addHandle(document, "touchmove", handleTouchEvent);
  Event.addHandle(document, "touchend", handleTouchEvent);
  Event.addHandle(document, "touchcancel", handleTouchEvent);
</script>
```
##### 手势事件
> gesturestart: 当一个手指已经按在屏幕上而另一个手指又触摸屏幕时触发  
gesturechange: 当触摸屏幕的任何一个手指的位置发生变化时触发  
gestureend: 当任何一个手指从屏幕上面移开时触发  
触摸事件和手势事件之间存在某种关系。当一个手指放屏幕上触发touchstart事件。如果另一个手指又放到屏幕上，则会先触发gesturestart事件，随后触发基于该手的touchstart事件。如果一个或者两个手指在屏幕上滑动，将会触发gesturechange事件。但只要有一个手指离开，就会触发gestureend事件，紧接着触发该手指的toucheend事件  
```html
<script>
  function handleGestureEvent(event){
    event = Event.getEvent(event);
    var output = document.getElementById("output");
    switch(event.type){
      case "gesturestart":
        output.innerHTML = "Gesture started (rotation= " + event.rotation + ", scale= " + event.scale + " )";
        break;
      case "gesturechange":
        output.innerHTML = "Gesture changed (rotation= " + event.rotation + ", scale= " + event.scale + " )";
        break;
      case "gestureend":
        output.innerHTML = "Gesture ended (rotation= " + event.rotation + ", scale= " + event.scale + " )";
        break;
    }
  }
  Event.addHandle(document, "gesturestart", handleGestureEvent);
  Event.addHandle(document, "gesturechange", handleGestureEvent);
  Event.addHandle(document, "gestureend", handleGestureEvent);
</script>
```
