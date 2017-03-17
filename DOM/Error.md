#### 错误处理与调试
```javascript
try{
  // 可能导致的代码
}cathc(error){
  // error.message
  console.log(error.message);
}
```
error对象中兼容浏览器的属性`message`能显示错误具体信息
