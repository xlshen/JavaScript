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
读写`DataView`的时候，要根据实际操作的数据类型，选择相应的`getter`和`setter`方法。
```javascript
    // getter           // setter
// 有符号8位整数
getInt8(byteOffset) setInt8(byteOffset, value)
// 无符号8位整数
getUint8(byteOffset) setUint8(byteOffset, value)
// 有符号16位整数
getInt16(byteOffset, littleEndian) setInt16(byteOffset, value, littleEndian)
// 无符号16位整数
getUint16(byteOffset, littleEndian) setUint16(byteOffset, value, littleEndian)
// 有符号32位整数
getInt32(byteOffset, littleEndian) setInt32(byteOffset, value, littleEndian)
// 无符号32位整数
getUint32(byteOffset, littleEndian) setUint32(byteOffset, value, littleEndian)
// 32位浮点数
getFloat32(byteOffset, littleEndian) setFloat32(byteOffset, value, littleEndian)
// 64位浮点数
getFloat64(byteOffset, littleEndian) setFloat64(byteOffset, value, littleEndian)
// 64位浮点数
```
所有的这些方法第一个参数都是一个字节偏移量，表示从哪个字节开始读取或者写入。记住，保存这些数据类型的数据，可能需要的不止1B。比如，无符号8位整数要用1B，而32位浮点数则用4B。使用DataView，就需要自己来管理这些细节，要明确知道自己的数据需要多少字节，并选择正确的读写方法。如：
```javascript
var buffer = new ArrayBuffer(20),
    view = new DataView(buffer),
    value;
view.setUint16(0, 25);
view.setUint16(0, 50); // 不能从第一个字节开始，因为16位整数要用2B
value = view.getUint16(0);
```
