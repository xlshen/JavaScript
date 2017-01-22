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
1. blur: 在元素失去焦点时触发。这个事件不会冒泡，所有浏览器都支持它。  
2. focus: 在元素获得焦点时触发。这个事件不会冒泡，所有浏览器都支持它。  
3. focusin: 在元素获得焦点时触发。这个事件和focus等价，但它冒泡。  
4. focusout: 在元素失去焦点时触发。这个事件和blur等价，但它冒泡。  

当焦点从页面中的一个元素移到另一个元素，会依次触发：  
1. focusout失去焦点元素触发  
2. focusin获得焦点元素触发  
3. blur失去焦点元素触发  
4. focus获得焦点元素触发  

