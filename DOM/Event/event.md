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
    stopPropagation: function(){
      if(event.stopPropagation){
        event.stopPropagation();
      }else{
        event.cancelBubble = true;
      }
    }
  }
```
