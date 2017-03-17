#### 错误处理与调试
```javascript
try{
  // 可能导致的代码
}catch(error){
  // error.message
  console.log(error.message);
}
```
error对象中兼容浏览器的属性`message`能显示错误具体信息
##### finally子句
finally语句无论如何都会执行，不论之前try，catch做什么，都会执行finally子句
```javascript
try{
  return 0;
}catch(error){
  return 1;
}finally{
  return 2; // 最终返回2
}
```
##### 错误类型
1. Error: 基类  
2. EvalError: 如果没有把eval()当作函数调用  
3. RangeError: 数值超出相应范围  
4. ReferenceError: 找不到对象   
5. SyntaxError: 语法错误  
6. TypeError: 执行某种操作时，变量类型不符合要求     
7. URIError: 使用encodeURI和decodeURI时，传递URI格式不正确   
```javascript
try{
  throw new TypeError("some");
}catch(e){
  if(e instanceof TypeError){
    // 
  }else if(e instanceof RangeError){
    //
  }else{
    // 其他
  }
}
```
##### 抛出错误
throw用于抛出错误，抛出错误时，必须给throw抛出一个值。
```javascript
throw 1;
throw true;
throw "2";
throw {"name": "xlshen"}
```
##### 创建自定义错误类型
常用的Error, TypeError, RangeError, ReferenceError类型
```javascript
function NewError(message){
  this.name = "newError";
  this.message = message;
}
NewError.prototype = new Error();
throw new NewError("Self Error");
```
##### 错误事件
任何没有通过try-catch处理的错误都会触发window的error事件。error事件不会创建event对象，它包含三个参数：错误消息、错误所在URL和行号。  
要指定error事件必须按照DOM0级事件处理，DOM2级不支持
```javascript
window.onerror = function(message, url, line){
  // message
  return false; // 阻止浏览器默认行为，不会返回错误信息
}
```
src为错误地址或者图像不能识别，像也会触发error事件
```javascript
var image = new Image();
Event.addHandle(window, "load", function(){
  alert("Image loaded!");
});
window.onerror = function(message, url, line){
  alert("Image error!");
}
image.src = "default.gif"; // 不存在的图片
```
