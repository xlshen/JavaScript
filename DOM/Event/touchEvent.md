#### 触摸与手势事件
> touchstart: 当手指触摸屏幕时触发；及时已经有一个手指放在了屏幕上也会触发  
touchmove: 当手指在屏幕上滑动时连续触发。在这个事件发生期间，调用event.preventDefault()可以阻止滚动  
touchend: 当手指从屏幕上移开时触发  
touchcancel: 当系统停止跟踪触摸时触发。

所有事件都会冒泡
