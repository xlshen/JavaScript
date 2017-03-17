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
  return 2;
}
```
