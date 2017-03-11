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
所有的这些方法第一个参数都是一个字节偏移量，表示从哪个字节开始读取或者写入。记住，保存这些数据类型的数据，可能需要的不止1B。比如，无符号8位整数要用1B，而32位浮点数则用4B。使用`DataView`，就需要自己来管理这些细节，要明确知道自己的数据需要多少字节，并选择正确的读写方法。如：
```javascript
var buffer = new ArrayBuffer(20),
    view = new DataView(buffer),
    value;
view.setUint16(0, 25);
view.setUint16(2, 50); // 不能从第一个字节开始，因为16位整数要用2B
value = view.getUint16(0);
```
用于读写16位及以上数值都有一个可选的参数`littleEndian`，它是一个布尔型。该参数表示读写数值时是否采用小端字节序（将数据的最低有效位保存在低内存地址中），而不是大端字节序（将数据的最低有效位保存在高内存地址中），如果不确定，就采取默认的大端字节序保存
```javascript
view.setUint16(0, 25);
view.getInt8(0); // 0
// 数据25以16位无符号整数形式被写入，字节偏移量为0。然后，再以8位有符号整数读取，得到0。这是因为25的二进制形式前8位都是0
```
虽然`DataView`能让我们在字节级别上读写数组缓冲器的数据，但我们必须知道自己将数据保存地址，需要多少字节。因此类型化视图应运而生！
##### 类型化视图
类型化视图一般也被称为`类型化数组`，因为它们除了元素必须是某种特定的数据类型外，与常规的数组无异。类型化数组有以下几种，它们都继承了`DataView`:
```javascript
Int8Array: 8位二补整数
Uint8Array: 8位无符号整数
Int16Array: 16位二补整数
Uint16Array: 16位无符号整数
Int32Array: 32位二补整数
Uint32Array: 32位无符号整数
Float32Array: 32位IEEE浮点数
Float64Array: 64位IEEE浮点数
```
由于这些视图都继承自`DataView`，因而可以使用相同的构造函数参数实例化。  
第一个参数要使用`ArrayBuffer`对象，第二个参数是作为起点的字节偏移量（默认0），第三个参数是要包含的字节数。三个参数只有第一个是必须的。
```javascript
// 创建一个新数组，使用整个缓冲器
var int8s = new Int8Array(buffer);
// 只使用从字节9开始的缓冲器
var int16s = new Int16Array(buffer, 9);
// 只使用从字节9到字节18的缓冲器
var uint16s = new Uint16Array(buffer, 9, 10);
```
能够制定缓冲器中可用的字节段，意味着能在同一个缓冲器中保存不同类型的数值，如：
```javascript
// 使用缓冲器的一部分保存8位整数，而在其他字节中保存16位整数
var int8s = new int8Array(buffer, 0, 10);
var uint16s = new int16Array(buffer, 11, 10);
```
每个视图构造函数都有一个名为`BYTE_PER_ELEMENT`的属性，表示类型化数组的每个元素需要多少字节。因此，`Uint8Array.BYTE_PER_ELEMENT`就是1，而`Float32Array.BYTE_PER_ELEMENT`为4。可以利用这个属性来辅助初始化。
```javascript
// 需要10个元素空间
var int8s = new Int8Array(buffer, 0, 10 * Int8Array.BYTE_PER_ELEMENT);
// 需要5个元素空间
var uint16s = new Uint16Array(buffer, int8s.byteOffset + int8s.byteLength, 5 * Uint16Array.BYTE_PER_ELEMENT);
```
除了这些优点之外，创建类型化视图还可以不用首先创建`ArrayBuffer`对象。只要传入希望数组保存的元素数，相应的构造函数就可以自动创建一个包含足够字节数的`ArrayBuffer`对象。如：
```javascript
// 创建一个数组保存10个8位整数（10字节）
var int8s = new Int8Array(10);
// 创建一个数组保存10个16位整数（20字节）
var int16s = new Int16Array(10);
// 把常规数组转化为类型化视图，只要把常规数组传入类型化视图的构造函数中：
// 创建一个数组保存5个8位整数（10字节）
var int8s = new Int8Array([10, 20, 30, 40, 50]);
for(var i = 0, len = int8s.length; i < len; i++){
    console.log("Position " + i + " is " + int8s[i]);
}
```
可以使用方括号语法为类型化视图的元素赋值。如果为相应元素制定的字节数不足，则实际保存的值为最大值的模。如无符号16位整数所能表示最大数值为65535，如果保存了65536，则实际上保存了0；如果保存了65537，那实际保存了1。
```javascript
var uint16s = new Uint16Array(10);
uint16s[0] = 65537;
console.log(uint16s[0]);
```
类型化数组还有个方法：`subarray()`,使用这个方法可以基于底层数组缓冲器的子集创建一个新视图。该方法接收两个参数：开始元素的索引和可选的结束元素的索引[非字节索引，而是元素索引]。返回的类型与调用该方法的视图类型相同。如：
```javascript
var uint16s = new Uint16Array(10),
    sub = uint16s.subarray(2, 5);
```
