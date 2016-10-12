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
&& VS || 详见
```
