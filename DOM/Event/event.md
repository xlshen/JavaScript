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
  1. `mousedown`  
  2. `mouseup`  
  3. `click`  
  4. `mousedown`
  5. `mouseup`
  6. `click`  
  7. `dblclick`  

##### 客户区坐标位置    
鼠标事件都是在浏览器适口中特定位置触发，这个位置信息保存在事件对象`clientX`和`clientY`属性中。所有浏览器都支持这两个属性，它们表示事件发生时鼠标指针在适口中的水平和垂直坐标。  
```javascript
  var div = document.getElementById("myDiv");
  Event.addHandler(div, "click", function(event){
    event = Event.getEvent(event);
    console.log("Client coordinates: " + event.clientX + ", " + event.clientY);
  });
```
##### 页面坐标位置    
页面坐标通过事件对象的`pageX`和`pageY`属性，可以获取事件在页面中什么位置发生，而非视窗位置。    
```javascript
  var div = document.getElementById("myDiv");
  Event.addHandler(div, "click", function(event){
    event = Event.getEvent(event);
    console.log("Client coordinates: " + event.pageX + ", " + event.pageY);
  });
  // IE8及之前不支持，需要通过计算获取
  Event.addHandler(div, "click", function(event){
    event = Event.getEvent(event);
    var pageX = event.pageX,
        pageY = event.pageY;
    if(pageY === undefined){
      pageY = event.clientY + (document.documentElement.scrollTop || document.body.scrollTop);
    }
    if(pageX === undefined){
      pageX = event.clientX + (document.documentElement.scrollLeft || document.body.scrollLeft);
    }
    console.log("Client coordinates: " + event.pageX + ", " + event.pageY);
  });
```
##### 屏幕坐标位置    
鼠标事件发生时相对于电脑屏幕的位置，通过`screenX`和`screenY`属性获取    
##### 修改键    
键盘上的某些键的状态可以影响所要采取的操作，这些修改键就是`shiftKey`，`ctrlKey`，`altKey`，`metaKey`(`Window`键或键)，这些属性都是布尔值，如果相应的键被按下，则为`true`，否值为`false`。当鼠标事件触发时，可以检测这几个属性的状态：    
```javascript
  Event.addHandler(div, "click", function(event){
    event = Event.getEvent(event);
    var keys = new Array();
    if(event.shiftKey){
      keys.push("shift");
    }
    if(event.ctrlKey){
      keys.push("ctrl");
    }
    if(event.altKey){
      keys.push("alt");
    }
    if(event.metaKey){
      keys.push("meta");
    }
    console.log("Keys: " + keys.join(", "));
   });
```
##### 相关元素  
`DOM`通过`event`对象的`relatedTarget`属性提供了相关元素的信息。这个属性只对于`mouseover`和`mouseout`事件才包含值；对于其他事件这个属性为`null`；`IE8`不支持`relatedTarget`属性，在`mouseover`事件触发时，`IE`的`fromElement`属性中保存了相关元素；在`mouseout`事件触发时，`IE`的`toElement`属性保存着相关元素。  
```javascript
  var Event = {
    getRelatedTarget: function(event){
      if(event.relatedTarget){
        return event.relatedTarget;
      }else if(event.toElement){
        return event.toElement;
      }else if(event.fromElement){
        return event.fromElment;
      }else{
        return null;
      }
    }
  }
```
##### 鼠标滚轮事件  
当用户通过鼠标滚轮与页面交互、在垂直方向上滚动页面时，就会触发`mousewheel`事件。这个事件可以再任何元素上面触发，最终冒泡到`document（IE8）`或`window（IE9、Opera、Chrome和Safari）`对象。  
其中`event`对象包含一个特殊的`wheelDelta`属性。当用户滚动鼠标滚轮时，`wheelDelta`是`120`的倍数；当用户向后滚动鼠标滚轮时，`wheelDelta`是`-120`的倍数。
##### 键盘和文本事件  
1. `keydown`：当用户按下键盘任意键触发，如果按住不放，重复触发  
2. `keypress`：当用户按下键盘上字符键触发，如果按住不放，重复触发  
3. `keyup`：当用户释放键盘上键触发  
只有一个文本事件：`textInput`，在文本出入文本框之前会触发`textInput`事件    

触发顺序：`keydown->keypress->keyup`，其中`keydown`和`keypress`都是再文本框变化之前被触发，而`keyup`是在文本框发生变化后触发。如果用户按下一个字符键不放，就会重复触发keydown和keypress事件  

如果用户按下一个非字符键，首先触发`keydown`事件，然后是`keyup`。
当发生`keydown`和`keyup`时，`event`对象的`keyCode`属性中包含一个代码，与键盘上一个特定的键对应。

##### textInput事件
"DOM3级事件"引入了一个新事件：textInput。当用户在可编辑区域中输入字符时就会触发这个事件。由于textInput考虑的是字符，因此它的event对象中包含一个data属性，这个属性的值就是用户输入的字符。
```javascript
  Event.addHandler(textbox, "textInput", function(event){
    event = Event.getEvent(event);
    console.log(event.data);
  });
```
##### readstatechange
```html
  <script>
    Event.addHandle(window, "load", function(event){
      var script = document.createElement("script");
      // var link = document.createElement("link");
      // link.type = "text/css";
      // link.rel = "stylesheet";
      event = Event.getEvent(event);
      var target = Event.getTarget(event);
      
      Event.addHandle(script, "readyStatechange", function(event){
         if(target.readyState == "loaded" || target.readyState == "complete"){
          Event.removeHandle(target, "readyStateChange", arguments.callee);
        }
      });
      script.src = "";
      document.body.appendChild(script);
    });
  </script>
```
