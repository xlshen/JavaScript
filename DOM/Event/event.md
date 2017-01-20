#### 事件
##### 跨浏览器事件对象
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
    getEvent: function(){
      
    }
  }
```
