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
6. loadend: 在通讯完成或者触发error、abort、load事件后触发
// 没有浏览器支持

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
##### 跨源资源共享
默认情况下，XHR只能访问与包含它的页面位于同一个域的资源。CORS(Cross-Origin Resource Sharing)跨源资源共享是W3C的一个工作草案，背后思想就是使用自定义的HTTP头部让浏览器与服务器沟通，从而决定请求或响应是成功或者失败
###### IE中CORS的实现
IE8中引入了`XDR(XDomainRequest)`类型，相比XHR对象更为安全可靠实现跨域通信，不同之处：
>1. Cookie不会随请求发出，也不会随响应返回
2. 只能设置头部信息中的Content-Type
3. 不能访问响应头信息
4. 只支持GET和POST请求

所有XDR都是异步的
```javascript
var xdr = new XDomainRequest();
xdr.onload = function(event){
  alert(xdr.responseText);
}
xdr.onerror = function(event){
  alert("Error");
}
xdr.open("GET", "exm.php"); // 只接收两个参数
xdr.send(null);
```
接收响应后，只能获取响应的文本数据；没有办法访问响应的state，只要响应有效就会触发load事件，如果失败就会触发error事件，终止请求abort();XDR对象也支持timeout属性和ontimeout事件处理程序。  
为了支持POST请求，XDR对象也提供contentType属性，用来表示发送信息的格式：
```javascript
xdr.contentType = "application/x-www-form-urlencoded";
xdr.send("name=value&age=18");
```
###### 其他浏览器对CORS的实现
Firefox 3.5+、Safari、Chrome、IOS版的Safari和Android的Webkit都通过XMLHttpRequest对象原生支持CORS。要请求另一个域的资源，只要在open()方法的URL中添加绝对URL地址即可。
```javascript
xhr.open("GET", "http://in.exm.php", true);
```
与IE中XDR对象不同，通过跨域的XDR对象可以访问响应的status和statusText属性，而且支持同步。但还有以下限制：
>1. 不能使用setRequestHeader()设置自定义头部信息
2. 不能接收和发送Cookie
3. 调用getAllResponseHeaders()方法总是返回空字符串

###### Preflighted Requests
CORS通过一种叫做Preflighted Requests验证机制支持开发人员使用自定义的头部、GET和POST之外的方法，以及不同类型的主体内容。在使用下列高级选项发送请求时，就会向服务器先发送一个Preflight请求，该请求使用OPTIONS方法。
>Origin: 发送请求源  
Access-Control-Request-Method: 请求自身使用的方法  
Access-Control-Request-Headers: （可选）自定义的头信息，多个头信息逗号分隔

```javascript
// 带自定义头信息的POST请求
Origin: http://github.xlshen.io
Access-Control-Request-Method: POST
Access-Control-Request-Headers: JS
```
发送该请求后，服务器会决定是否允许该类请求。通过设置以下字段：
>Access-Control-Allow-Origin: 允许的发送请求源域    
Access-Control-Allow-Methods: 允许的方法，多个以逗号分隔  
Access-Control-Allow-Headers: 允许的头部信息，多个以逗号分隔  
Access-Control-Max-Age: 应该将这个Preflight请求缓存多久，多久之内不用再次发送该Preflight请求（秒）

```javascript
Access-Control-Allow-Origin: http://github.xlshen.io
Access-Control-Allow-Methods: POST,GET
Access-Control-Allow-Headers: JS
Access-Control-Max-Age: 3600
```

Preflight之后会把该请求缓存起来，为此的代价就是多了一次HTTP请求。
###### 带凭据的请求
默认情况下，跨域请求是不携带凭证（Cookie，HTTP认证等）。通过withCredentials属性设置为true，可以指定某个请求应该发送凭证。如果服务器接收到请求，会发送响应：
```javascript
Access-Control-Allow-Credentials: true
```

如果服务器响应中没有该设置，则浏览器不会把响应交给JavaScript（responseText为空，status为0，而且会调用error事件处理程序）。另外，服务器可以在Preflight请求的响应中发送该字段，表示允许源请求发送带凭证的请求。【IE10及更早版本不支持】
###### 跨浏览器CORS
```javascript
function createCORSRequest(method, url){
  var xhr = new XMLHttpRequest();
  if("withCredentials" in xhr){
    xhr.open(method, url, true);
  }else if(typeof XDomainRequest != "undefined"){
    xhr = new XDomainRequest();
    xhr.open(method, url);
  }else{
    xhr = null;
  }
  return xhr;
}
var request = createCORSRequest("GET", "http://github.xlshen.io");
if(request){
  xhr.onload = function(){
    // responseText
  }
  xhr.send(null);
}
```
XDR和XHR共有的方法：
>abort(), onerror, onload, responseText, send()

##### 其他跨域技术
利用DOM中能够执行跨域请求的功能，在不依赖XHR对象的情况下也能发送某种请求。
###### 图像Ping
一个网页加载图片不用担心跨域问题，这也是广告跟踪浏览量的主要方式。图像Ping与服务器进行简单、单向的跨域通信，请求的数据可以通过查询字符串发送，服务器响应通常是图像或者204（No Content），通过图像Ping得不到任何数据。但是可以通过onload的onerror事件监听什么时候接收到。
```javascript
var image = new Image();
image.onload = image.onerror = function(){
  alert("Done");
}
image.src = "/xlshen.gif?name=xlshen"; // 发送name参数给服务器
```
该方法缺点：只能发送GET方法；无法获取服务器响应文本
###### JSONP
JSONP是JSON with padding（填充式JSON），是应用JSON的一种新方法。JSONP两部分组成：回调函数和数据。回调函数是当响应之后执行的函数，其名字是在请求时指定的，数据就是传入到回调函数中的JSON数据。
```javascript
function jsonpCallback(response){
  // response
}
var script = document.creatElement("script");
script.src = "http://...?callback=jsonpCallback";
document.body.insertBefore(script, document.body.firstChild);
```
与图像Ping相比，JSONP可以直接方法响应内容，但还有亮点不足：如果访问域家在的代码存在安全问题，就会造成不良后果；另外，不能轻易确定JSONP请求失败，为此，开发人员一般使用计时器进行不精确判断。
###### Comet
人称：“服务器推送”，Ajax是客户端向服务器推送，而Comet是服务器向客户端推送技术。Comet能够让信息实时推送到页面，非常适合实时通讯领域。  
有两种实现Comet方法：`长轮询`和`流【长连接】`  
1. 传统轮询（短轮询／Ajax），浏览器定时向服务器发送请求更新数据。长轮询把短轮询反过来，页面向服务器发送一个请求，然后服务器链接一直打开，直到有数据可以发送，发送完数据之后，链接关闭，随即有发送一个请求到服务器，这一过程页面打开期间一直不间断。  
短轮询和长轮询之前都要先对服务器发送请求，区别在于短轮询是服务器立即发送响应，不管数据是否有效，而长轮询是等待发送响应。轮询的优势是所有浏览器都支持，因为使用XHR和setTimeout()就能实现。  
2. 第二种流行的是HTTP流。流不同于两种轮询，因为它在页面的整个生命周期内只使用一个HTTP连接。具体来说，浏览器向服务器发送一个请求，服务器保持连接打开，然后周期性的向浏览器发送数据。在Firefox、Safari、Opera和Chrome中，通过监听readystatechange事件及检测readyState是否为3，就可以利用XHR对象实现HTTP流。  
区别：对于流方式，客户端发起连接就不会断开连接，而是由服务器端进行控制。当服务器端有更新时，刷新数据，客户端进行更新；而对于长轮询，当服务器端有更新返回，客户端先断开连接，进行处理，然后重新发起连接。  
iframe流
iframe流方式是在页面中插入一个隐藏的iframe，利用其src属性在服务器和客户端之间创建一条长链接，服务器向iframe传输数据（通常是HTML，内有负责插入信息的javascript），来实时更新页面。  
iframe流方式的优点是浏览器兼容好，Google公司在一些产品中使用了iframe流，如Google Talk。  
Iframe是html标记，这个标记的src属性会保持对指定服务器的长连接请求，服务器端则可以不停地返回数据，相对于第一种方式，这种方式跟传统的服务器推则更接近。  
在第一种方式中，浏览器在收到数据后会直接调用JS回调函数，但是这种方式该如何响应数据呢？可以通过在返回数据中嵌入JS脚本的方式，如“<script type="text/javascript">js_func(“data from server ”)</script>”，服务器端将返回的数据作为回调函数的参数，浏览器在收到数据后就会执行这段JS脚本。  
```javascript
function createStreamingClient(url, progress, finished){
  var xhr = new XMLHttpRequest(),
      recevied = 0;
  xhr.open("GET", url, true);
  xhr.onreadystatuschange = function(){
    var result;
    if(xhr.readyState == 3){
      result = xhr.responseText.substring(recevied);
      recevied += result.length;
      progress(result); // 执行回调函数
    }else if(xhr.readyState == 4){
      finished(xhr.responseText);
    }
  };
  xhr.send(null);
  return xhr;
}
var client = createStreamingClient("stream.php", function(data){
  alert(data);
}, function(data){
  alert("Done");
});
```
为了简化Comet的实现，为Comet创建了两个新的接口。
###### 服务器发送事件
SSE(Server-Sent Events)是围绕Comet交互推出的API或者模式。SSE API用于创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据。服务器响应的MIME类型必须是text／event-stream，而且是浏览器中的JavaScript API能解析格式的输出。SSE支持短轮询，长轮询和HTTP流，而且能在断开连接时自动确定何时重新连接。支持SSE浏览器：Firefox 6+,Safari 5+, Opera 11+,Chrome和IOS 4+ Safari。  
I. `SSE API`
```javascript
// 创建一个新的EventSource对象
var source = new EventSource("myevents.php"); // 传入URL必须与页面同源（域、端口）
```
EventSource实例有一个readyState属性，值为0表示正在连接到服务器，值为1表示打开了连接，值为2表示关闭了连接。  
另外三个事件：
1. open: 建立连接时触发  
2. message: 接收服务器新数据时触发  
3. error: 在无法建立连接时触发
```javascript
source.onmessage = function (event) {
  var data = event.data; // 处理数据
}
```
默认情况下，EventSource对象会保持与服务器的活动连接，如果连接断开，还会重新连接。这意味着SSE适合长轮询和HTTP流。如果向强制断开连接不再重连，可以调用close()方法。  
II. 事件流  
服务器事件通过一个持久的HTTP响应发送，这个响应的MIME类型为text／event-stream。响应的格式是纯文本，简单情况是每个数据项前面都有data:,如：
```html
data: foo

data: bar

data:foo
data:bar
```
上述响应中，事件流中的第一个message事件返回的event.data值为“foo”，第二个是”bar”，第三个是“foo\nbar”(中间有换行符)。只有在包含data:的数据后面有空行时，才会触发message事件。通过id:前缀可以给特定的事件指定一个关联的ID，这个ID行位于data:行前或者后面：
```html
data: foo
id: 1
```
设置ID后，EventSource对象会跟踪上一次触发事件，如果断开，下次会向服务器发送一个包含Last-Event-ID的特殊HTTP头部请求，方便服务器直到下一次该触发哪个事件。在多次连接的事件流中，这种机制可以确保浏览器正确的顺序接收连接的数据段。
###### Web Sockets
`Web Sockets`在一个持久连接上提供全双工、双向通信。首先浏览器发送一个HTTP请求到服务器，建立连接后会使用HTTP转换为`Web Sockets`协议，使用标准的HTTP服务器无法实现`Web Sockets`，只有支持该协议的专门服务器才能正常工作。  
`Web Sockets`使用自定义的协议，所以URL模式也不同，未加密的连接不再是`http://`，而是`ws://`;加密的连接也不是`https://`，而是`wss://`。在使用时必须带上这个模式，将来可能支持其他模式。  
使用自定义协议的好处：客户端和服务器通信发送非常少量数据，而不是HTTP那样字节级别开销，因此非常适合移动端开发。支持浏览器：Firefox 6+,Safari 5+,Chrome和IOS 4+ Safari。
I. `Web Sockets API`
```javascript
// 实例话WebSockets对象并传入连接的URL
var socket = new WebSockets("ws://www.exam.com/server/php");
```
【注：传入的URL必须是绝对URL同源策略对Web Sockets不适用，因此可以打开任何站点，至于是否与某个域中的页面通信，完全取决于服务器】
实例化后WebSockets之后，浏览器马上尝试创建连接，与XHR类似，WebSockets也有一个表示当前状态的readyState属性：
>1. WebSockets.OPENING(0): 正在建立连接
2. WebSockets.OPEN(1): 已经建立连接
3. WebSockets.CLOSING(2): 正在关闭连接
4. WebSockets.CLOSE(3): 已经关闭连接

WebSockets没有readystatechange事件；不过，它有其他事件，对应不同状态。readyState的值永远从0开始。要关闭Web Socket连接，调用close()方法
```javascript
socket.close();
```
II. 发送和接收数据  
```javascript
var socket = new WebSockets("ws://www.exam.com/server.php");
socket.send("First fire.");
```
因为Web Sockets只能通过连接发送纯文本数据，对于复杂的数据发送前先序列化。  
当服务器向客户端发送数据时，WebSockets对象触发message事件，这个事件与其他协议类似，返回的数据保存在event.data属性中
```javascript
socket.onmessage = function(event){
  var data = event.data; // 处理数据
}
```
III. 其他事件  
>1. open: 成功建立连接时触发
2. error: 发生错误时触发，连接不能持续
3. close: 连接关闭时触发

WebSockets对象不支持DOM2级事件监听，必须用DOM0级语法分别定义每个事件处理程序。
```javascript
socket.onopen = function(){
  alert("连接建立！")；
}；
socket.onerror = function () {
  alert("连接出错!")；
};
socket.onclose = function (event) {
  // event对象获取属性wasClean, code, reason
  alert("连接关闭！");
  console.log("Clean ? " + event.wasClean + " Code = " + event.code + " Reason = " + event.reason);
}
```
只有close事件中event对象有额外信息：wasClean（布尔型，表示连接是否明确关闭）、code（服务器返回的数值状态码）和reason（字符串，服务器发回的消息）。
###### SSE VS Web Sockets
1. 是否自由建立和维护Web Sockets服务器？  
2. 需不需要双向通信？如果只需要读取服务器数据，SSE比较容易实现。如果必须双向通信，Web Sockets更好。当然也可以组合使用XHR和SSE实现双向通信。
