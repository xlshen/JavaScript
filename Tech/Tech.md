### 小技巧系列
#### jQuery里：
###### 1. 净化数组([].slice.call(this))
```javascript
var arr = [1, 2, 3];
arr['name'] = 'xlshen';
console.log(arr); // [1, 2, 3, name: "xlshen"]
[].slice.call(this); // [1, 2, 3]
```
