#### 事件
跨浏览器事件对象:
```javascript
  var Event = {
    addHandle: function(element, type, handler){
      if(element.addEventListener){
        element.addEventListener(type, handler, false);
      }else if(element.attachEvent){
        element.attachEvent("on" + type, handler);
      }else{
        element["on" + type] = handler;
      }
    },
    removeHandler: function(element, type, handler){
      if(element.removeEventListener){
        element.removeEventListener(type, handler, false);
      }else if(element.detachEvent){
        element.detach("on" + type, handler);
      }else{
        element["on" + type] = null;
      }
    },
    getEvent: function(event){
      return event ? event : window.event;
    },
    getTarget: function(event){
      return event.target ? event.target : event.srcElement;
    },
    preventDefault: function(event){
      if(event.preventDefault){
        event.preventDefault();
      }else{
        event.returnValue = false;
      }
    },
    stopPropagation: function(event){
      if(event.stopPropagation){
        event.stopPropagation();
      }else{
        event.cancelBubble = true;
      }
    }
  }
```
##### 焦点事件  
1. `blur`: 在元素失去焦点时触发。这个事件不会冒泡，所有浏览器都支持它。  
2. `focus`: 在元素获得焦点时触发。这个事件不会冒泡，所有浏览器都支持它。  
3. `focusin`: 在元素获得焦点时触发。这个事件和focus等价，但它冒泡。  
4. `focusout`: 在元素失去焦点时触发。这个事件和blur等价，但它冒泡。  

当焦点从页面中的一个元素移到另一个元素，会依次触发：  
1. `focusout`失去焦点元素触发    
2. `focusin`获得焦点元素触发     
3. `blur`失去焦点元素触发      
4. `focus`获得焦点元素触发    

##### 鼠标和滚轮事件  
+ `click`: 用户点击鼠标和按下回车键时触发
+ `dblclick`: 用户双击鼠标时触发
+ `mousedown`: 用户按下任意鼠标键触发。不能通过键盘触发该事件
+ `mouseenter`: 鼠标光标从元素外部首次移动到元素范围内触发，这个事件不冒泡，而且在光标移动到后代元素上不会触发。
+ `mouseleave`: 鼠标移动到元素范围之外时触发，这个事件不冒泡，而且光变移动到后代元素上面不会触发。
+ `mousemove`: 当鼠标指针在元素内部移动时重复触发，不能通过键盘触发该事件。
+ `mouseout`: 鼠标移动到另一个元素时触发，移入的元素可以是元素的外部，也可以是元素的子元素。
+ `mouseover`: 鼠标位于一个元素上方时触发。
+ `mouseup`: 用户释放鼠标按钮时触发。  
【注：除了`mouseenter`和`mouseleave`所有鼠标事件都支持冒泡，也可以被取消】    
只有在同一个元素上面相继触发`mousedown`和`mouseup`事件，才会触发`click`事件；如果其中一个被取消就不会触发`click`事件。类似的，只有触发两次`click`事件才会触发一次`dblclick`事件，这四个人事件触发顺序：
1. mousedown  
2. mouseup  
