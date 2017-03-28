### 小技巧系列
#### jQuery里：
##### 1. [].slice.call(this)【净化数组】
```javascript
var arr = [1, 2, 3];
arr['name'] = 'xlshen';
console.log(arr); // [1, 2, 3, name: "xlshen"]
[].slice.call(this); // [1, 2, 3]
```
##### 2. &&的妙用
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
