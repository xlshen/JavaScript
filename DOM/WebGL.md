#### WebGL
WebGL涉及的复杂计算需要提前知道数值的精度，而标准的JavaScript数值无法满足需要。为此，WebGL引入了一个概念：`类型化数组`。类型化数组也是数组，只不过其中的元素被设置为特定类型的值。  
类型化数组核心就是一个名为`ArrayBuffer`的类型。每个`ArrayBuffer`对象表示的只是内存中制定的字节数，但不会制定这些字节用于保存什么类型的数据。通过它所能做的，就是为了将来使用而分配一定数量的字节。如：  
```javascript
var buffer = new ArrayBuffer(20);
```
创建了`ArrayBuffer`对象后，能够通过该对象获得信息只有它包含的字节数，方式是访问其`byteLength`属性：
```javascript
var bytes = buffer.byteLength;
```
##### 视图
使用`ArrayBuffer`(数组缓冲器类型)的一种特别的方式用来作数组缓冲器`视图`。其中，最常见的视图是`DataView`通过它可以选择`ArrayBuffer`中一小段字节。为此，可以在创建`DataView`时传入一个`ArrayBuffer`、一个可选的字节偏移量（从该字节开始选择）和一个可选的要选择的字节数。如：
```javascript
// 基于整个缓冲器创建一个新视图
var view = new DataView(buffer);
// 创建一个开始于字节9的新视图
var view = new DataView(buffer, 9);
// 创建一个从字节9开始到字节18结束的新视图
var view = new DataView(buffer, 9, 10);
```
实例化后，`DataView`对象把字节偏移量以及字节长度信息分别保存在`byteOffset`和`byteLength`属性中
```javasript
console.log(view.byteOffset);
console.log(view.byteLength);
```
