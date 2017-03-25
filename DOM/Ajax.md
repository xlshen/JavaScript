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
>1. responseText: 响应主体返回的文本
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
>1. Accept: 浏览器能处理的内容类型
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
POST请求要发送数据主体，数据格式不限。发送POST请求要在send()方法中传入某些数据。最早是发送XML DOM文档，传入的文档经序列化发送到服务器。也可以传入任何想发送到服务器的字符串。可以模仿表单提交：将`Content-Type`头部信息设置为：`application/x-www-form-urlencoded`(表单提交时内容类型)，其次是以适当的格式创建一个字符串
```javascript
function submitData(){
  var xhr = new XMLHttpRequest();
  xhr.onreadystatuschange = function(){
    if(xhr.readyState == 4){
      if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
        alert(xhr.responseText);
      }else{
        alert("Request Failed");
      }
    }
  }
  xhr.open("POST", "exm.php", true);
  xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
  var form = document.getElementById("info");
  xhr.send(serialize(form));
}
```
与GET请求相比，POST请求会消耗的资源更多，从性能上看，发送相同的数据，GET请求是POST请求速度的两倍。
##### XMLHttpRequest2 级
###### FormData
FormData是为序列化表单定义，为创建和表单格式相同的数据提供了便利。
```javascript
var data = new FormData();
data.append("name", "xlshen");
```
`append()``方法接收两个参数，属性名和属性值。对应表单的name和value。可以添加任意多个键值对。并且可以直接传入表单元素。
```javascript
var data = new FormData(document.forms[0]);
xhr.send(data);
```
不必明确声明请求头信息，XHR对象能够识别传入的FormData实例。
###### 超时设定
IE8为XHR添加了`timeout`属性，表示等待多少毫秒后请求终止。如果设定timeout属性，在等待了设定时间之后服务器还没有响应，则会调用`ontimeout`事件处理程序。该功能被纳入XMLHttpRequest2中
```javascript
xhr.open("POST", "exm.php", true);
xhr.timeout = 1000;  // 仅IE8+支持该属性。。。
xhr.ontimeout = function(){
  alert("Request Failed in 1000");
}
xhr.send(null);
```
如果调用ontimeout事件处理程序时，readyState也变成4触发onreadystatuschange事件处理程序，如果超时终止请求后再访问status属性将会报错，所以将xhr.status判断放到try-catch中进行。
###### overrideMimeType()方法
`overrideMimeType()`方法被用于修改XHR响应的MIME类型。被XMLHttpRequest2纳入规范。修改MIME类型决定了XHR对象如何处理响应。如果服务器返回的MIME响应类型是text／plain，但数据是XML，根据MIME类型，即使数据是XML，responseXML属性仍然是null，通过修改MIME可以正常处理该响应
```javascript
xhr.open("POST", "exm.php", true);
xhr.overrideMimeType("text/xml"); // 一定要在send()方法之前调用该方法
xhr.send(null);
```
##### 进度事件
>1. loadstart: 在接收到服务器响应数据的第一个字节时就触发
2. progress: 在接收数据期间持续触发
3. error: 在请求出错时触发
4. abort: 调用abort()之后触发
5. load: 在接收到完整数据时触发 // IE8+只支持load
6. loadend: 在通讯完成或者触发error、abort、load事件后触发 // 没有浏览器支持
###### load事件
`load`事件用来替代`onreadystatuschange`事件，load事件处理程序接收`event`参数，其`target`属性指向XHR对象，可以通过该属性访问XHR对席那个的所有方法和属性。然而并非浏览器都实现了适当的事件对象。结果我们还是要通过判断`status`来决定当前状态：
```javascript
xhr.onload = function(event){
  // xhr.status >= 200 ...
  if(if((event.target.status >= 200 && event.target.status < 300) || event.target.status == 304){ // 直接通过xhr.status可以获取
    alert(event.target.responseText);
  }else{
    alert("Request Failed");
  } )
}
```
###### progress事件
在接收数据之间持续触发，也会接受一个event参数，target属性指向XHR对象。但除此之外还包含三个属性：`lengthComputable`、`loaded`、`total`。
>lengthComputable: 进度信息是否可用（布尔型）  
loaded: 已经接受的字节数  
total: 根据Content-Length响应头信息确定的预期字节数
```javascript
xhr.onprogress = function(event){
  var statusLen = document.getElementById("status");
  if(event.lengthComputable){ // 必须首先判断是否可计算
    statusLen.innerHTML = event.loaded / event.total + "%";
  }
}
```
