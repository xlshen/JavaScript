### 小技巧系列
#### jQuery里：
###### 1. [].slice.call(this)【净化数组】
```javascript
var arr = [1, 2, 3];
arr['name'] = 'xlshen';
console.log(arr); // [1, 2, 3, name: "xlshen"]
[].slice.call(this); // [1, 2, 3]
```
