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
var sth = function(element){
  this.name = element;
  this.handleEvent = function(_event){
      // 此处可以使用event对象
      console.log(this.name);
  }
  event.addEventListerner("change", this, false); // this指代的为调用对象
}
var s = new sth(kktext);
```
