#### 触摸与手势事件
> touchstart: 当手指触摸屏幕时触发；及时已经有一个手指放在了屏幕上也会触发  
touchmove: 当手指在屏幕上滑动时连续触发。在这个事件发生期间，调用event.preventDefault()可以阻止滚动  
touchend: 当手指从屏幕上移开时触发  
touchcancel: 当系统停止跟踪触摸时触发。

所有事件都会冒泡，除了常见的DOM属性外，触摸事件还包含下列三个用于跟踪触摸的属性：
> touches: 表示当前跟踪触摸操作的Touch对象的数组    
targetTouchs: 特定于事件目标的Touch对象的数组    
changeTouches: 表示自上次触摸以来发生了什么变化的Touch对象的数组    
每个Touch对象包含下列属性：    
clientX: 触摸目标在视图中x坐标    
clientY: 触摸目标在视图中y坐标  
identifier: 触摸唯一的ID  
pageX: 触摸目标在页面x坐标  
pageY: 触摸目标在页面y坐标  
screenX: 触摸目标在屏幕中的x坐标  
screenY: 触摸目标在屏幕中的y坐标  
target: 触摸DOM节点目标  
