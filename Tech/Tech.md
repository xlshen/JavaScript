### Advanced JavaScript
#### [].slice.call(this) [净化数组]
```javascript
var arr = [1, 2, 3];
arr['name'] = 'xlshen';
console.log(arr); // [1, 2, 3, name: "xlshen"]
[].slice.call(this); // [1, 2, 3]
arr.concat(); // 可以直接copy一个arr新数组，可以保留原数组arr
```
#### &&的妙用
```javascript
var options = 'XXX'; // XXX是'memory'或者非'XXX'值
memory = options === 'XXX' && data; // 全等于memory = (options === 'XXX') && data;
//注：该表达式存储memory的值，如果options为XXX，则memory为data，否则为data的值
```
[详见`&& VS ||`](https://github.com/xlshen/JavaScript/issues/6 "&& VS ||")
#### 事件代理
```javascript
var Sth = function(element){
  this.name = "Sth good.";
  this.handleEvent = function(_event){
      // 此处可以使用event对象
      console.log(this.name);
  }
  element.addEventListerner("change", this, false); // this指代的为调用对象
}
var s = new Sth(kktext);
```
#### 安全类型检测
解决问题办法是调用Object原生的toString()方法，都会返回一个[object NativeConstructorName]格式字符串。每个类内部都有一个[[class]]属性，这个属性就指定了上述字符串中的构造函数名。
```javascript
function isArray(value){
    return Object.prototype.toString.call(value) == "[object Array]";
}
function isFunction(value){
    return Object.prototype.toString.call(value) == "[object Function]";
}
function isRegExp(value){
    return Object.prototype.toString.call(value) == "[object RegExp]";
}
var isNativeJSON = window.JSON && Object.prototype.toString.call(JSON) == "[object JSON]";
```
#### 作用域安全的构造函数
```javascript
function Base(large){
    if(this instanceof Base){
        this.large = large;
        this.getArea = function(){
            return this.large;
        };
    }else{
        return new Base(large);
    }
}
function BaseMin(width, height){
    Base.call(this, 10);
    this.width = width;
    this.height = height;
    this.getArea = function(){
        return this.width * this.height;
    };
}
BaseMin.prototype = new Base();
var min = new BaseMin(30, 40);
console.log(min.large); // undefined
```
#### 懒性载入函数
1.
```javascript
function createXHR(){
    if(typeof XMLHttpRequest != "undefined"){
        return new XMLHttpRequest();
        // createXHR = function(){
        //   return new XMLHttpRequest();
        // }
    }else if(typeof ActiveXObject != "undefined"){
        // createXHR = function(){
            if(typeof arguments.callee.ActiveXString != "string"){
                var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0","MSXML2.XMLHttp"], i, len;
                for(i = 0, len = versions.length; i < len; i++){
                    try{
                        new ActiveXObject(versions[i]);
                        arguments.callee.ActiveXString = versions[i];
                        break;
                    }catch(ex){}
                }
            }
            return new ActiveXObject(arguments.callee.ActiveXString);
        // }
    }else{
        // createXHR = function(){
            throw new Error("No XHR object available");
        // }
    }
    // return createXHR();
}
```
2.
```javascript
var createXHR = (function(){
    if(typeof XMLHttpRequest != "undefined"){
        return function(){
            return new XMLHttpRequest();
        }
    }else if(typeof ActiveXObject != "undefined"){
        return function(){
            if(typeof arguments.callee.ActiveXString != "string"){
                var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0","MSXML2.XMLHttp"], i, len;
                for(i = 0, len = versions.length; i < len; i++){
                    try{
                        new ActiveXObject(versions[i]);
                        arguments.callee.ActiveXString = versions[i];
                        break;
                    }catch(ex){}
                }
            }
            return new ActiveXObject(arguments.callee.ActiveXString);
        }
    }else {
        return function(){
            throw new Error("No XHR object available");
        }
    }
})();
```
#### 函数绑定
```javascript
var handler = {
    message: "Event handled.",
    handleClick: function(event){
        alert(this.message);
    }
}
Event.addHandle(btn, "click", handler.handleClick); // handleClick中this指向的是DOM元素
/**
 * 函数作用域bind函数简单实现
 * @param  {Function} fn      函数作用域需要绑定的函数
 * @param  {Object}   context 绑定作用域
 * @return 函数执行结果
 */
function bind(fn, context){
    return function(){
        return fn.apply(context, arguments);
    }
}
Event.addHandle(btn, "click", bind(handler.handleClick, handler)); // handleClick中this指向的是DOM元素
// ES5原生支持bind()函数
Event.addHandle(btn, "click", hander.handleClick.bind(handler); // handleClick中this指向的是DOM元素
```
#### 函数柯里化
步骤：调用另一个函数并为其传入要柯里化的函数和必要参数。
```javascript
/**
 * 函数柯里化
 * @param  {Function} fn 需要柯里化的函数
 * @return 执行传入函数
 */
function curry(fn){
    var args = [].slice.call(arguments, 1);
    return function(){
        var innerArgs = [].slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return fn.apply(null, finalArgs);
    }
}
function add(num1, num2){
    return num1 + num2;
}
var curriedAdd = curry(add, 5, 10);
console.log(curriedAdd());
```
函数柯里化常常作为函数绑定的一部分含在其中，构造出更复杂的bind()函数。
```javascript
/**
 * bind函数，应用函数柯里化
 * @param  {Function} fn      柯里化函数
 * @param  {Object}   context 上下文
 * @return 在上下文执行函数结果
 */
function bind(fn, context){
    var args = [].slice.call(arguments, 2);
    return function(){
        var innerArgs = [].slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return fn.apply(context, finalArgs);
    }
}
var handler = {
    message: "Event handled.",
    handleClick: function(name, event){
        alert(name + this.message);
    }
}
Event.addHandle(btn, "click", bind(hander.handleClick, hander, "btn"));
```
#### 数组分块
```javascript
/**
 * 数组分块函数
 * @param  {Array} array   需要分块处理的数组数据
 * @param  {Function} process 处理函数
 * @param  {Object} context 执行上下文
 */
function chunk(array, process, context){
    setTimetout(function(){
        var item = array.shift();
        process.call(context, item);
        // 数组还有元素的话
        if(array.length > 0){
            setTimetout(arguments.callee, 100);
        }
    }, 100);
}
var data = [1, 12, 123, 1234, 12345, 123456, 1234567, 12345678, 123456789, 1234567890];
function printValue(item){
    var div = document.getElementById("chunk");
    div.innerHTML += item + "<br />";
}
chunk(data.concat(), printValue); // data.concat();防止改变原来数组元素
```
#### 函数节流
防止代码不间断的连续重复调用执行
```javascript
function throttle(method, context){
    clearTimeout(method.Id);
    method.Id = setTimeout(function(){
        method.call(context);
    }, 100);
}
function resizeElement(){
    var div = document.getElementById("chunk");
    div.style.height = document.body.clientHeight;
}
window.onresize = function(){
    throttle(resizeElement)
}
```
#### 自定义事件
观察者模式：主体&观察者  
主体负责发布事件，同时观察者通过订阅这些事件来观察该主体。关键概念：主体不知道观察者的任何事情，也就说它可以独立存在即使观察者不存在。另一方面，观察者知道主体并能注册事件的回调函数。在DOM上，DOM是主体，事件处理代码是观察者。  
```javascript
function EventTarget(){
    this.handlers = {};
}
EventTarget.prototype = {
    constructor: EventTarget,
    /**
     * 注册给定类型事件处理程序
     * @param  {String} type    事件类型名称
     * @param  {Function} handler 事件处理程序
     */
    addHandler: function(type, handler){
        if(typeof this.handlers[type] = "undefined"){
            this.handlers[type] = [];
        }
        this.handlers[type].push(handler);
    },
    /**
     * 事件触发
     * @param  {Event} event
     */
    fire: function(event){
        if(!event.target){
            event.target = this;
        }
        if(this.handlers[event.type] instanceof Array){
            var handlers = this.handlers[event.type];
            for(var i = 0, len = handlers.length; i < len; i++){
                handlers[i](event);
            }
        }
    },
    /**
     * 移除事件处理程序
     * @param  {String} type    事件类型名称
     * @param  {Function} handler 事件处理程序
     */
    removeHandler: function(type, handler){
        if(this.handlers[type] instanceof Array){
            var handlers = this.handlers[type];
            for(var i = 0, len = handlers.length; i < len; i++){
                if(handlers[i] === handler){
                    break;
                }
            }
            handlers.splice(i, 1);
        }
    }
};
function handleMessage(event){
    console.log("Message received: " + event.message);
}
var target = new EventTarget();
target.addHandler("message", handleMessage);
target.fire({type: "message", message: "Hello World"});
target.removeHandler("message", handleMessage);
```
#### 拖放
```javascript
/**
 * 单例模式
 * @return [Object] {enable, disable}
 */
var DragDrop = function(){
  var dragging = null,
      diffX = 0, // 防止鼠标点击跳动
      diffY = 0; // 防止鼠标点击跳动
  function handleEvent(event){
    event = Event.getEvent(event);
    var target = Event.getTarget(event);
    // 判断事件类型
    switch (event.type) {
      case "mousedown":
        if(target.className.indexOf("draggable") > -1){
          dragging = target;
          diffX = event.clientX - target.offsetLeft;
          diffY = event.clientY - target.offsetTop;
        }
        break;
      case "mousemove":
        if(dragging !== null){
          dragging.style.left = (event.clientX - diffX) + "px";
          dragging.style.top = (event.clientY - diffY) + "px";
        }
        break;
      case "mouseup":
        dragging = null;
        break;
      default:
    }
  }
  return {
    enable: function(){
      Event.addHandle(document, "mousedown", handleEvent);
      Event.addHandle(document, "mousemove", handleEvent);
      Event.addHandle(document, "mouseup", handleEvent);
    },
    disable: function(){
      Event.removeHandler(document, "mousedown", handleEvent);
      Event.removeHandler(document, "mousemove", handleEvent);
      Event.removeHandler(document, "mouseup", handleEvent);
    }
  }
}();
```
#### 添加自定义事件
```javascript
var DragDrop = function(){
    var dragging = null,
        diffX = 0,
        diffY = 0,
        dragdrop = new EventTarget(); // 自定义事件类型
    function handleEvent(event){
        var target = event.target;
        switch (event.type) {
            case "mousedown":
                if(target.className.indexOf("draggable") > -1){
                    dragging = target;
                    diffX = event.clientX - target.offsetLeft;
                    diffy = event.clientY - target.offsetTop;
                }
                /**
                 * 定义事件类型
                 */
                dragdrop.fire({
                    type: "dragstart",
                    target: dragging,
                    x: event.clientX,
                    y: event.clientY
                });
                break;
            case "mousemove":
                if(dragging !== null){
                    target.style.left = (event.clientX - diffX) + "px";
                    target.style.top = (event.clientY - diffY) + "px";
                }
                dragdrop.fire({
                    type: "drag",
                    target: dragging,
                    x: event.clientX,
                    y: event.clientY
                });
                break;
            case "mouseup":
                dragdrop.fire({
                    type: "dragend",
                    target: dragging,
                    x: event.clientX,
                    y: event.clientY
                });
                dragging = null;
                break
            default:

        }
    }
    dragdrop.enable = function(){
        Event.addHandle(document, "mousedown", handleEvent);
        Event.addHandle(document, "mousemove", handleEvent);
        Event.addHandle(document, "mouseup", handleEvent);
    };
    dragdrop.disable = function(){
        Event.removeHandler(document, "mousedown", handleEvent);
        Event.removeHandler(document, "mousemove", handleEvent);
        Event.removeHandler(document, "mouseup", handleEvent);
    };
    return dragdrop;
}();
DragDrop.addHandler("dragstart", function(event){
    var status = document.getElementById("status");
    status.innerHTML = "Dragging " + event.target.id;
});
```
