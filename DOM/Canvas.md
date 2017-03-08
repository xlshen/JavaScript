#### Canvas
使用`<canvas>`元素必须先设置其`width`和`height`属性，指定可以绘图的区域大小。出现在开始和结束标签中的内容是在浏览器不支持时显示
```html
  <canvas id="drawing" width="200" height="200">A drawding of sth.</canvas>
```
绘图首先取得绘图上下文
```javascript
 var drawing = document.getElementById("drawing");
 if(drawing.getContext){ // 检测浏览器是否支持
   var context = drawing.getContext("2d");
 }
```
要取得`<canvas>`上绘制的图像，使用`toDataURL()`方法。该方法接受一个参数，即图像的MIMEl类型格式，而且适合创建图像的任何上下文。
```javascript
if(drawing.getContext){
 var imgURL = drawing.toDataURL("image/png");
 var image = document.createElement("img");
 image.src = imgURL;
 document.body.appendChild(image);
}
```
##### 2D上下文
> 2D上下文坐标开始于`<canvas>`元素的左上角，原点(0,0)。所有坐标值都是基于这个原点计算的。x越大越靠右，y越大越靠下。

###### 填充和描边
2D上下文的两种基本绘图操作是填充和描边。填充就是用指定的样式填充图形；描边就是只在图形的边缘画线。操作的结果取决于两个属性：fillStyle和strokeStyle。 这两个属性值可以是字符串、渐变对象或模式对象，而且它们默认值都是"#000000"。如果为它们指定表示颜色的字符串，可以使用CSS中指定颜色值的任何格式
```javascript
var context = drawing.getContext("2d");
context.strokeStyle = "red";
context.fillStyle = "#0000ff";
```
