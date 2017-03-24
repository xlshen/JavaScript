#### Ajax
##### XMLHttpRequest对象
```javascript
// IE7+、Firefox、Opera、Chrome和Safari都支持原生XHR对象
var xhr = new XMLHttpRequest();
```
###### XHR用法
`open()`方法，接受三个参数：请求类型、请求URL、是否异步请求布尔值
```javascript
xhr.open("get", "example.php", false); // 可以是绝对路径，但必须同域
```
调用`open()`方法并不会发送请求，只是启动一个请求用来发送
`send()`方法，发送特定请求，接受一个参数：请求主题发送的数据。如果不需要必须传null
```javascript
xhr.send(null);
```
发送该请求后，因为是同步的，必须等到服务器响应，响应数据会自动填充XHR对象的属性，属性如下：
> 1. responseText: 响应主体返回的文本
2. responseXML: 如果响应内容类型是“text/xml”、”application/xml”，这个属性保存响应数据的XML DOM文档
3. status: 响应的HTTP状态
4. statusText: HTTP状态说明

收到响应后，第一步检查status属性确定响应是否成功，可以通过下面简单检查：
```javascript
if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
  alert(xhr.responseText);
}else{
  alert("Request was unsuccessful: " + xhr.status);
}
```
上述例子是同步操作，大部分我们都用异步请求来进行，此时可以通过检查XHR对象的readyState属性来确定响应阶段：
>0: 为初始化。还没有调用open()方法  
1: 启动。已经调用open()方法，但未调用send()方法  
2: 发送。已经调用send()方法，但未收到响应  
3: 接收。已经接收部分响应数据  
4: 完成。已经全部接收响应数据，可以使用  

通过监听readystatechange事件可以检查readystate值，每次readystate变化都会触发readystatechange事件。调用open()之前指定readystatechange事件处理程序
```javascript
var xhr = new XMLHttpRequest();
Event.addHandle(xhr, "readystatechange", function(){
  if(xhr.readystate == 4){
    if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
      alert(xhr.responseText);
    }else{
      alert("Request was unsuccessful: " + xhr.status);
    }
  }
});
xhr.open("get", "exm.php", true);
xhr.send(null);
```
如果接收到响应前可以调用`abort()`方法来取消异步请求
```javascript
xhr.abort();
```
###### HTTP头信息
XHR对象提供了操作请求和响应头信息的方法。默认情况下，发送XHR请求同时会发送下面头信息：
> 1. Accept: 浏览器能处理的内容类型
2. Accept-Charset: 浏览器能显示的字符集
3. Accept-Encoding: 浏览器能处理的压缩编码
4. Accept-Language: 浏览器当前设置的语言
5. Connection: 浏览器与服务器之间链接的类型
6. Cookie: 当前页面设置的Cookie
7. Host: 发送请求页面所在的域
8. Referer: 发送请求页面的URI
9. User-Agent: 浏览器用户代理字符串

使用`setRequestHeader()`方法可以设置自定义的请求头信息，该方法接收两个参数：头部字段名称、头部字段值，并且必须在调用`open()`方法之后，`send()`方法之前调用！
```javascript
xhr.open("get", "exm.php", true);
xhr.setRequestHeader("SelfHeader", "Value"); // 建议不要使用浏览器默认的头部信息
xhr.send(null);
```
使用`getResponseHeader()`方法并传入头部字段信息，可以取得相应的响应头部信息。而调用`getAllResponseHeaders()`方法可以取得一个包含所有头部信息的长字符串。
```javascript
var myHeader = xhr.getResponseHeader("SelfHeader");
var allHeaders = xhr.getAllResponseHeaders();
```
###### GET请求
对XHR而言，传入URL末尾的查询字符串必须经过正确编码`encodeURIComponent()`才行。
```javascript
xhr.open("get", "exm.php?name=value&age=value", true);
/**
 * 辅助向URL末尾添加字符串
 * @param {String} url   
 * @param {String} name  
 * @param {String} value
 * @return {String} url
 */
function addURLParam(url, name, value){
  url += (url.indexOf("?") === -1 ? "?" : "&");
  url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
  return url;
}
```
###### POST请求
POST请求要发送数据主体，数据格式不限。发送POST请求要在send()方法中传入某些数据。最早是发送XML DOM文档，
