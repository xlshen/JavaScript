#### 原生拖放
##### 拖放事件
有些事件在拖放元素上触发，有些事件是在放置目标上触发。
> 1. dragstart
> 2. drag
> 3. dragend

按下鼠标拖动触发`dragstart`事件，通过`ondragstart`事件处理，触发dragstart后，即会触发`drag`事件，而且在拖动过程中会持续触发。该事件和`mouseover`事件类似，停止拖动触发`dragend`事件。  
当某个元素被拖动到一个有效的目标上，会触发：
> 1. dragenter
> 2. dragover
> 3. dragleave或drop

只要元素被拖动到目标上时就会触发dragenter事件，然后dragover事件，在目标元素上移动时持续触发。当元素拖出目标外就会触发dragleave事件。如果元素被放置到目标中，触发drop事件而不是dragleave事件。上述事件都是在目标元素上触发。
##### 自定义放置目标
虽然所有元素都支持放置目标事件，但这些元素默认是不允许放置的。但我们可以把任何元素变成可放置的目标，方法为重写dragenter和dragover事件的默认行为：
```javascript
var dropstart = document.getElementById("droptarget");
Event.addHandle(dragtarget, "dragover", function(event){
  Event.preventDefault(event);
});
Event.addHandle(dragtarget, "dragenter", function(event){
  Event.preventDefault(event);
});
// 兼容FF3.5+，也要取消drop默认行为
Event.addHandle(dragtarget, "drop", function(event){
  Event.preventDefault(event);
});
```
##### dataTransfer对象
事件对象的属性，用于从被拖动的元素向放置目标传递字符串格式的数据，只能在拖动事件处理程序中访问该属性。
>1. getData(kk)
> 2. setData(kk, value) // kk是一个字符串，取值“text”等各种MIME值

```javascript
// 设置和接收文本
event.dataTransfer.setData("text", "Sth");
var text = event.dataTransfer.getData("text");
```
保存的`tranTransfer`对象只有在`drop`时间处理程序中读取，如果`drop`事件也没有取到，说明`dataTransfer`对象已经被销毁，数据丢失
##### dropEffect和effectAllowed
通过`dataTransfer`对象的`dropEffect`和`effectAllowed`属性可以确定被拖元素和作为放置目标元素能够接收什么操作
`dropEffect`: 确定被拖放元素能执行哪些放置行为
> 1. none: 不能把拖放元素放到这。除了文本框之外所有元素的默认值
> 2. move: 应该把拖放元素移动到放置目标
> 3. copy: 应该把拖放元素复制到放置目标
> 4. link: 防止目标会打开拖放的元素(拖动元素必须是一个链接)

`要使用dropEffect属性，必须在ondragenter事件处理程序中设置`  
dropEffect属性只有配合effectAllowed属性才有用。effectAllowed属性表示允许拖动元素的哪种dropEffect，值如下：
> 1. uninitialized: 没有给拖动元素设置任何放置行为
> 2. none: 被拖动的元素不能有任何行为
> 3. copy: 只允许值为"copy"的dropEffect
> 4. link: 只允许值为"link"的dropEffect
> 5. move: 只允许值为"move"的dropEffect
> 6. copyLink: 允许值为"copy"和"link"的dropEffect
> 7. copyMove: 允许值为"copy"和"move"的dropEffect
> 8. linkMove: 允许值为"link"和"move"的dropEffect
> 9. all: 允许任意的dropEffect

`要使用effectAllowed属性，必须在ondragstart事件处理程序中设置`
##### 可拖动
默认，`图像`、`链接`和`文本`是可拖动的。
HTML5提供了一个`draggable`属性，表示元素是否可以拖动，图像和链接自动设置为true，其他默认false
```html
<img src="default.png" draggable="false" alt="default"/>
```
`[用拖拽实现不完美拖动弹框]`
```html
<style>
    #mask{
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        height: 100%;
        width: 100%;
        background-color: rgba(0, 0, 0, .4);
    }
    #dragTest{
        position: fixed;
        width: 300px;
        top: 50%;
        left: 50%;
        -webkit-transform: translate(-50%,-50%);
        -moz-transform: translate(-50%,-50%);
        transform:translate(-50%,-50%);
        background-color: #fff;
    }
    header{
        font-size: 20px;
        font-weight: bold;
        text-align: center;
        cursor: move;
        color: #666;
        border-bottom: 1px solid #ddd;
        line-height: 2;
    }
    .dragContent{
        text-align: center;
    }
</style>
<div id="mask"></div>
<div id="dragTest" draggable="true">
    <header>测试头</header>
    <div class="dragContent">
        <h3>drag测试案例</h3>
    </div>
</div>
<script>
    var leftNum, topNum;
    document.getElementById("dragTest").addEventListener("dragstart", function(event){
        // 需要增加document的dragover事件，否则无法准确获取clientX和clientY属性
        document.addEventListener("dragover", function(event){
            topNum = event.clientY;
            leftNum = event.clientX;
            console.log(event.clientX, event.clientY);
        }, false);
    }, false);
    document.getElementById("dragTest").addEventListener("drag", function(event){
        document.getElementById("dragTest").style.top = topNum + "px";
        document.getElementById("dragTest").style.left = leftNum + "px";
    }, false);
</script>
```
#### 媒体元素
`<audio>`与`<video>`
```html
<video src="" controls=true autoplay width="100%" height="200" poster="img.png">
  <source src="">
  不支持HTML5播放器
</video>
<audio src="" controls=true autoplay width="100%" height="200">
  <source src="">
  不支持HTML5播放器
</audio>
```
##### 自定义媒体播放器
使用`<audio>`和`<video>`元素的`play()`和`pause()`方法，可以手动控制播放媒体文件。组合使用属性，事件和这两个方法，可以很容易创建一个自定义的媒体播放器。
```html
<div class="mediaplayer">
  <div class="video">
    <video id="player" src="movie.mov" poster="img.png" width="200" height="200">
      不支持
    </video>
  </div>
  <div class="controls">
    <input type="button" value="Play" id="video-btn">
    <span id="curtime">0</span>/<span id="duration">0</span>
  </div>
</div>
<script>
  var player = document.getElementById("player"),
      btn = document.getElementById("video-btn"),
      curtime = document.getElementById("curtime"),
      duration = document.getElementById("duration");
  // 更新播放时间
  duration.innerHTML = player.duration; // 加到load事件里面，如果视频没加载，此值无法获取
  Event.addHandle(btn, "click", function(event){
    if(player.paused){
      player.play();
      btn.value = "Pause";
    }else{
      player.pause();
      btn.value = "Play";
    }
  });
  setInterval(function(){
    curtime.innerHTML = player.currentTime;
  },200);
</script>
```
##### Audio类型
`<Audio>`元素还有一个原生的JavaScript构造函数Audio，可以在任何时候播放音频。从DOM角度看，Audio和Image很相似，但Audio不必像Image那样必须插入到文档中，只要创建一个新实例，并传入音频文件即可。
```javascript
var audio = new Audio("player.mp3");
Event.addHandle(audio, "canplaythrough", function(){
  audio.play();
});
```
##### 历史状态管理
> HTML5新增了`pushState()`和`replaceState()`方法管理历史记录    
1. `pushState`接收三个参数：状态对象、新状态标题和相对URL【必须和原URL同源】   
2. `replaceState`接收两个参数：状态对象，新状态标题  
```javascript
history.pushState({
  "first": 1,
  "second": 2
}, "page", "/index.html");
```
执行`pushState()`方法后，新状态被压入状态栈，浏览器地址栏改为新设置的地址，但不会去请求服务器。第二个参数现无浏览器实现，推荐默认null。  
`pushState()`方法创建了新历史状态，浏览器会出现“后退”操作，点击后退或者JavaScript执行后退操作，触发`popstate`事件，该事件有个`state`属性，该属性保存着`pushState()`方法传入的第一个参数对象【执行pushState()和replaceState()方法时并不会触发popstate事件】。
```javascript
Event.addHandle(window, "popstate", function(event){
  var state = event.state;
  if(state){ // 回撤到第一个页面state属性为null！
    //
  }
});
```
执行`replaceState()`方法会直接替换掉当前URL，但是不会在历史栈中创建新状态
```javascript
window.onpopstate = function(event) {
  alert("location: " + document.location + ", state: " + JSON.stringify(event.state));
};
history.pushState({page: 1}, "title 1", "?page=1"); // 压入历史栈，替换URL
history.pushState({page: 2}, "title 2", "?page=2"); // 压入历史栈，替换URL
history.replaceState({page: 3}, "title 3", "?page=3"); // 替换URL，不压历史栈，这个操作相当于把上一操作pushState操作抹掉了
history.back(); // alerts "location: http://example.com/example.html?page=1, state: {"page":1}" // 因为replaceState原因，历史栈上一状态为第一次操作内容
history.back(); // alerts "location: http://example.com/example.html, state: null // 回到最初状态，所以state为null
history.go(2);  // alerts "location: http://example.com/example.html?page=3, state: {"page":3} // 回到replaceState操作时状态
```
#### 离线应用与客户端存储
##### 离线检测
`navigator.onLine`属性检测设备能否访问网络返回true or false，同时HTML5还定义了两个事件：`online`和`offline`:当网络从离线变为在线或者在线变为离线时会触发这两个事件。支持离线检测的浏览器IE6+（只支持navigator.online）、Firefox、Safari4、Opera 10.6、Chrome、IOS3.2 Safari和Android Webkit。  
##### 应用缓存
HTML5的应用缓存简称：appcache，专门为开发离线Web应用而设计的。Appcache就是从浏览器的缓存中分出来一块缓存区。要想在这个缓存区中保存数据，可以使用一个描述文件：manifest file，列出要下载和缓存的资源。  
application cahce是将大部分图片资源、js、css等静态资源放在manifest文件配置中。当页面打开时通过manifest文件来读取本地文件或是请求服务器文件。  
使用缓存接口可为您的应用带来以下三个优势：  
I. 离线浏览 – 用户可在离线时浏览您的完整网站  
II. 速度 – 缓存资源为本地资源，因此加载速度较快   
III. 服务器负载更少 – 浏览器只会从发生了更改的服务器下载资源  
```javascript
CACHE MANIFEST
#Comment
file.js
file.css
```
可以在<html>中manifest属性指定这个文件的路径：
```javascript
<html manifest="/offline.manifest">
```
一个简单离线页面应用包含以下
```html
// index.html
<html manifest="clock.manifest">
  <head>
    <title>AppCache Test</title>
    <link rel="stylesheet" href="clock.css">
    <script src="clock.js"></script>
  </head>
  <body>
    <p><output id="clock"></output></p>
    <div id="log"></div>
  </body>
</html>
// clock.manifest
CACHE MANIFEST
#VERSION 1.0
CACHE:
clock.css
clock.js
// JavaScript 代码
<script type="text/javascript">
    var appCache = window.applicationCache;
    switch (appCache.status) {
        case appCache.UNCACHED: // UNCACHED == 0
            return 'UNCACHED';
            break;
        case appCache.IDLE: // IDLE == 1
            return 'IDLE';
            break;
        case appCache.CHECKING: // CHECKING == 2
            return 'CHECKING';
            break;
        case appCache.DOWNLOADING: // DOWNLOADING == 3
            return 'DOWNLOADING';
            break;
        case appCache.UPDATEREADY:  // UPDATEREADY == 4
            return 'UPDATEREADY';
            break;
        case appCache.OBSOLETE: // OBSOLETE == 5
            return 'OBSOLETE';
            break;
        default:
            return 'UKNOWN CACHE STATUS';
            break;
    };
</script>
// 为了通过编程更新cache，首先调用 applicationCache.update()。这将会试图更新用户的 cache（要求manifest文件已经改变）。最后，当 applicationCache.status 处于 UPDATEREADY 状态时， 调用applicationCache.swapCache()，旧的cache就会被置换成新的。
<script type="text/javascript">
    var appCache = window.applicationCache;
    appCache.update(); // Attempt to update the user’s cache.
    if (appCache.status == window.applicationCache.UPDATEREADY) {
      appCache.swapCache();  // The fetch was successful, swap in the new cache.
    }
</script>
```
#### 存储数据
##### Cookie
HTTP Cookie，简称cookie。  
I. 限制：每个域cookie总数是有限的，不过浏览器之间各不相同，多小为4096B-1（4095B）的长度限制。  
> IE7+每个域名最多50个    
Firefox每个域最多50个   
Opera每个域最多30个  
Safari和Chrome每个域数量没有硬性规定  

当超过这个限制后再设置cookie，浏览器会清除之前设置的cookie。
II. cookie的构成  
> 名称（不区分大小写）: 必须经过URL编码！  
值: 必须URL编码！  
域：有效域，说明cookie在哪些域有效，如果没有明确规定，则表示设置cookie那个域   
路径： 指定域中的路径，如果路径设置了："https://xlshen.github.io/index/" ，则只有这个路径下的文件可以访问cookie，"https://xlshen.github.io/" 路径下的文件不可以访问。  
失效时间： cookie被删除的时间戳。默认情况下，浏览器会话结束后自动删除，但是可以手动设置过期时间，该值为GMT格式日期(调用toGMTString())。  
安全标志： 制定后，cookie只有在使用了SSL链接的时候才能发送到服务器。例如：cookie信息可以发送给:"https://xlshen.github.io/" ，而不是："http://xlshen.github.io/" 。  

III. JavaScript中Cookie
`document.cookie`返回当前页面所有可用cookie的字符串，一系列由分号分隔，所有都是经过编码的结果，必须通过`decodeURIComponent()`进行解码。
```javascript
var CookieUtil = {
  get: function(name){
    var cookieName = encodeURIComponent(name) + "=",
        cookieStart = document.cookie.indexOf(cookieName),
        cookieValue = null;
    if(cookieStart > -1){
      var cookieEnd = document.cookie.indexOf(";", cookieStart);
      if(cookieEnd === -1){
        cookieEnd = document.cookie.length;
      }
      cookieValue = decodeURIComponent(document.cookie.substring(cookieStart + cookieName.length, cookieEnd));
    }
    return cookieValue;
  },
  set: function(name, value, expires, path, secure, secure){
    var cookieText = encodeURIComponent(name) + "=" + encodeURIComponent(value);
    if(expires instanceof Date){
      cookieText += "; expires=" + expires.toGMTString();
    }
    if(path){
      cookieText += "; path=" + path;
    }
    if(domain){
      cookieText += "; domain=" + domain;
    }
    if(secure){
      cookieText += "; secure"
    }
    document.cookie = cookieText;
  },
  unset: function(name, path, domain, secure){
    this.set(name, "", new Date(0), path, domain, secure);
  }
};
```
IV. 子cookie  
为了绕开浏览器单域名下cookie个数限制，我们可以使用子cookie概念，子cookie的单个cookie值是由多个子cookie键值对组成。如：`name=name1=value&name2=value2&name3=value3`，为了更好的兼容此调用方式，我们改写下CookieUtil对象。
```javascript
var subCookieUtil = {
  get: function(name, subName){
    var subCookies = this.getAll(name);
    if(subCookies){
      return subCookies[subName];
    }else{
      return null;
    }
  },
  getAll: function (name) {
    var cookieName = encodeURIComponent(name) + "=",
        cookieStart = document.cookie.indexOf(cookieName),
        cookieValue = null,
        cookieEnd,
        subCookies,
        i,
        parts,
        result = {};
    if(cookieStart > -1){
      cookieEnd = document.cookie.indexOf(";", cookieStart);
      if(cookieEnd === -1){
        cookieEnd = document.cookieEnd.length;
      }
      cookieValue = document.cookie.substring(cookieStart + cookieName.length, cookieEnd);
    }
    if(cookieValue.length > 0){
      subCookies = cookieValue.split("&");
      for(i = 0, len = subCookies.length; i < len; i++){
        parts = subCookies[i].split("=");
        result[decodeURIComponent(parts[0])] = decodeURIComponent(parts[1]);
      }
      return result;
    }
    return null;
  },
  set: function(name, subName, value, expires, path, domain, secure){
    var subcookies = this.getAll(name) || {};
    subcookies[subName] = value;
    this.setAll(name, subcookies, expires, path, domain, secure);
  },
  setAll: function(name, subcookies, expires, path, domain, secure){
    var cookieText = encodeURIComponent(name) + "=",
        subcookieParts = new Array(),
        subName;
    for(subName in subcookies){
      if(subName.length > 0 && subcookies.hasOwnProperty(subName)){
        subcookieParts.push(encodeURIComponent(subName) + "=" + encodeURIComponent(subcookies[subName]));
      }
    }
    if(subcookieParts.length > 0){
      cookieText += subcookieParts.join("&");
      if(expires instanceof Date){
        cookieText += "; expires=" + expires.toGMTString();
      }
      if(path){
        cookieText += "; path=" + path;
      }
      if(domain){
        cookieText += "; domain=" + domain;
      }
      if(secure){
        cookieText += "; secure"
      }
    }else{
      cookieText += "; expires=" + (new Date(0)).toGMTString();
    }
    document.cookie = cookieText;
  },
  unset: function(name, subName, path, domain, secure){
    var subcookies = this.getAll(name);
    if(subcookies){
      delete subcookies[subName];
      this.setAll(name, subcookies, null, path, domain, secure);
    }
  },
  unsetAll: function(){
    this.setAll(name, null, new Date(0), path, domain, secure);
  }
};
```
##### Web Storage
`sessionStorage`和`localStorage`:
> 1. clear()： 删除所有值
2. getItem(name): 获取指定名称name的值
3. key(index): 获得index位置处值的名称
4. removeItem(name): 删除由name设置一个对应的值
5. setItem(name, value): 设置name及对应的值

属性length可以获取键值对的多少。  
作用域：localStorage只要在相同的协议、相同的主机名、相同的端口下，就能读取/修改到同一份localStorage数据。  
sessionStorage比localStorage更严苛一点，除了协议、主机名、端口外，还要求在同一窗口（也就是浏览器的标签页）  
域名限制：由于浏览器的安全策略，localstorage是无法跨域的，也无法让子域名继承父域名的localstorage数据，这点跟cookies的差别还是蛮大的。  
##### IndexedDB
设计思想：创建一套API，方便保存和读取JavaScript对象，同时还支持查询和搜索。  
IndexedDB设计操作完全是异步进行的，因此，大多数操作都是以请求方式进行，浏览器未实现完全统一。  
```html
<!-- IndexedDB 实例 -->
<body onload=" init() ">
    <input type="button" value="新增几条数据" onclick=" Tinsert(); "/>
    <input type="button" value="查找一条记录" onclick=" Tselect(); "/>
    <input type="button" value="删除一条记录" onclick=" Tdelete(); "/>
    <input type="button" value="更新一条记录" onclick=" Tupdate(); "/>
    <input type="button" value="清除所有表数据" onclick=" Tclear(); "/>
    <input type="button" value="删除本地数据库" onclick=" Ddelete('SISO'); "/>
</body>
```
```javascript
(function(){
    var dbObject = {};
    dbObject.init = function(params){
        this.db_name = params.db_name;
        this.db_version = params.db_version;
        this.db_store_name = params.db_store_name;
        if (!window.indexedDB)
        {
            window.alert("你的浏览器不支持IndexDB,请更换浏览器");
        }

        var request = indexedDB.open(this.db_name,this.db_version);
        //打开数据失败
        request.onerror = function(event)
        {
            alert("不能打开数据库,错误代码: " + event.target.errorCode);
        };
        request.onupgradeneeded = function(event)
        {
            this.db = event.target.result;
            this.db.createObjectStore(dbObject.db_store_name);
        };
        //打开数据库
        request.onsuccess = function(event)
        {
            //此处采用异步通知. 在使用curd的时候请通过事件触发
            dbObject.db = event.target.result;
        };
    };
    /**
     * 增加和编辑操作
     */
    dbObject.put = function(params,key)
    {
        //此处须显式声明事务
        var transaction = dbObject.db.transaction(dbObject.db_store_name, "readwrite");
        var store = transaction.objectStore(dbObject.db_store_name);
        var request = store.put(params,key);
        request.onsuccess = function(){
            alert('添加成功');
        };
        request.onerror = function(event){
            console.log(event);
        }
    };
    /**
     * 删除数据
     */
    dbObject.delete = function(id)
    {
        // dbObject.db.transaction.objectStore is not a function
        request = dbObject.db.transaction(dbObject.db_store_name, "readwrite").objectStore(dbObject.db_store_name).delete(id);
        request.onsuccess = function(){
            alert('删除成功');
        }
    };

    /**
     * 查询操作
     */
    dbObject.select = function(key)
    {
        //第二个参数可以省略
        var transaction = dbObject.db.transaction(dbObject.db_store_name,"readwrite");
        var store = transaction.objectStore(dbObject.db_store_name);
        // 不使用游标简单查询
        // if(key)
        //     var request = store.get(key);
        // else
        //     var request = store.getAll();
        // 使用游标查询
        var request = store.openCursor();
        request.onsuccess = function (event) {
          var cursor = event.target.result,
              updateRequest, // 游标更新操作请求对象
              deleteRequest; // 游标删除操作请求对象
          if(cursor){
            console.log(JSON.stringify(cursor.value)); // value is an Object
          }
          if(cursor.key === 1){
            value = cursor.value;
            value.isbn = 000000;
            updateRequest = cursor.update(value); // 使用游标更新内容
            deleteRequest = cursor.delete(value); // 使用游标删除内容
            /**
             * continue(key): key可选，表示继续开始下一个请求，如果指定key，直接跳到key所在的项
             * advance(count): 前进count指定的项数
             *
             * continue()和advance()共用一个onsuccess事件处理函数
             */
            cursor.continue(2); //游标移动到下一项;key可选，如果存在跳到指定key值
            updateRequest.onsuccess = function () {
              // update success
            };
            deleteRequest.onsuccess = function () {
              // delete success
            }
          }
        }
        // 使用游标范围界定筛选区域
        var keyRange = window.IDBKeyRange,
            /**
             * IDBKeyRange
             * only(key): 和get(key)一个效果
             * lowerBound(key, Boolean): 指定游标下界，游标开始的地方，表示游标从key值开始；true表示跳过开始值，默认false
             * upperBound(key, Boolean): 指定游标上界，游标从开头不能超过key指定的值，true表示不包含key值，默认false
             * Bound(key, key, Boolean, Boolean): 指定游标上下界
             */
            boundRange = keyRange.bound(1, 3, false, false); // 选定介于1和3之间的数据，包含1和3
        /**
         * IDBCursor
         * NEXT: 默认值，下一个
         * NEXT_NO_DUPLICATE： 下一个非重复的项
         * PREV： 上一个
         * PREV_NO_DUPLICATE: 上一个非重复的项
         */
        request = store.openCursor(boundRange, IDBCursor.NEXT_NO_DUPLICATE); // 跳过重复的选项

        // 创建索引
        /**
         * createIndex(name, keyname, {unique: Boolean}[optional]);
         * name: 索引的名字
         * keyname: 索引属性的名字
         * unique: true表示索引唯一性，false表示不唯一
         * @return IDBIndex实例
         */
        var index = store.createIndex("index", "title", {unique: false});
        // 获取指定的索引
        var index = store.index("index"), // index()方法获取指定的索引
            request = index.openCursor(), // 在索引上创建游标，和对象存储空间上调用一样
            /**
             * openKeyCursor() [创建返回主键的游标]
             * @return event.target.key [索引键]
             * @return event.target.value [主键]
             */
            request = index.openKeyCursor(),
            /**
             * 根据给定索引取得主键
             * getKey(key) [key: 索引键]
             * @return IBDIndex对象
             * 属性： event.target.key [索引键]
             * 属性： event.target.value [主键]
             * 属性： name [索引名称]
             * 属性： keyPath 带查看
             * 属性： objectStore [对象存储空间]
             * 属性： unique [索引键是否唯一]
             */
            request = index.getKey("title"),
        store.deleteIndex("index"); // 删除指定索引
    };
    /**
     * 清除整个对象存储(表)
     */
    dbObject.clear = function()
    {
        var request = dbObject.db.transaction(dbObject.db_store_name,"readwrite").objectStore(dbObject.db_store_name).clear();
        request.onsuccess = function(){
            alert('清除成功');
        }
    };
    window.dbObject = dbObject;
})();
  function init() {
      var dbParams = new Object();
      dbParams.db_name = "shen";
      dbParams.db_version = "2";
      dbParams.db_store_name = "Test";
      dbObject.init(dbParams);
  }

  function Tinsert() {
      // 填入初始值
      dbObject.put({ title: "QM", author: "F", isbn: 123456 }, 1);
      dbObject.put({ title: "WB", author: "F", isbn: 234567 }, 2);
      dbObject.put({ title: "BN", author: "B", isbn: 345678 }, 3);
  }

  function Tselect() {
      dbObject.select(3);
  }

  function Tupdate() {
      dbObject.put({ title: "QW", author: "L", isbn: 123456 }, 1);
  }

  function Tdelete() {
      dbObject.delete(3);
  }

  function Tclear() {
      dbObject.clear();
  }
  function Ddelete(name){
    var DBDeleteRequest = indexedDB.deleteDatabase(name);
    DBDeleteRequest.onsuccess = function(event) {
      console.log("Database deleted successfully");
      console.log(event.result); // should be undefined
    };
  }
```
