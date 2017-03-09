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
###### 绘制矩形
矩形是唯一一个可以直接在2D上下文中绘制的形状。方法包括：fillRect()、 strokeRect()和clearRect()。这三个方法都接收4个参数：矩形x坐标，矩形y坐标，矩形宽度，矩形高度。参数单位都是像素。  
fillRect()方法在画布上绘制的矩形会填充指定的颜色，填充的颜色通过fillStyle属性指定
```javascript
context.fillStyle = "#ff0000";
context.fillRect(10, 10, 50, 50);
context.fillStyle = "rgba(0, 0, 255, 0.5)";
context.fillRect(30, 30, 50, 50);
```
strokeRect()方法在画布上回执的矩形会使用指定的颜色描边。描边颜色通过strokeStyle属性指定。
```javascript
context.strokeStyle = "#ff0000";
context.strokeRect(10, 10, 50, 50);
context.strokeStyle = "rgba(0, 0, 255, 0.5)";
context.strokeRect(30, 30, 50, 50);
// lineWidth属性控制线条宽度，可以是任意整数值
// lineCap属性控制线条末端形状平头、圆头还是方头("butt", "round", "square")
// lineJoin属性控制线条相交是圆交、斜交还是斜接("round", "bevel", "miter")
```
clearRect()方法用于清除画布上矩形区域
```javascript
context.clearRect(40, 40, 10, 10);
```
###### 绘制路径
要绘制路径，首先必须调用`beginPath()`方法，表示要开始绘制路径。然后，在通过调用下面方法来实际绘制路径：  
> 1. arc(x, y, radius, startAngle, endAngle, counterclockwise):以(x, y)为圆心绘制一条弧线，半径为radius(像素)，起始和结束角度（弧度）分别为startAngle和endAngle。最后一个是否按逆时针计算，值为false表示按照顺时针  
2. arcTo(x1, y1, x2, y2, radius): 从上一个点开始绘制一条弧线，到(x2, y2)为止，并且以给定的半径radius穿过(x1, y1)  
3. bezierCurveTo(c1x, c1y, c2x, c2y, x, y): 从上一点开始绘制一条曲线，到(x, y)为止，并且以(c1x, c1y)和(c2x, c2y)为控制点 //三次贝塞尔曲线  
4. lineTo(x, y): 从上一点绘制一条直线，到(x, y)为止  
5. moveTo(x, y): 将绘图游标移动到(x, y)，不画线  
6. quadratiocCurveTo(cx, cy, x, y): 从上一点开始绘制一条二次曲线，到(x, y)为止，并且以(cx, cy)为控制点 // 二次贝塞尔曲线  
7. rect(x, y, width, height): 从点(x, y)开始绘制一个矩形，宽度和高度分别由width和height指定。这个方法绘制的是矩形路径，而不是strokeRect()和fillRect()所绘制的独立形状

创建了路径后，接下来如果想绘制一条连接路径起点的线条，可以调用`closePath()`。如果路径已经完成，想用`fillStyle`填充它，可以调用`fill()`方法。另外，还可以调用`stoke()`方法对路径描边，描边使用的是`strokeStyle`。最后调用`clip()`，可以创建一个剪切区域。
```javascript
context.beginPath(); // 开始绘制
context.arc(100, 100, 99, 0, 2*Math.PI, false); // 绘制外圆
context.moveTo(194, 100);
context.arc(100, 100, 94, 0, 2*Math.PI, false); // 绘制内圆
context.moveTo(100, 100);
context.lineTo(100, 15);
context.moveTo(100, 100);
context.lineTo(35, 100);
context.closePath();
context.stroke(); // 描边
```
`isPointInPath()`方法可以判断一个点是否在绘制的路径上
###### 绘制文本  
主要两个方法：fillText()和strokeText()。都接收四个参数：要绘制的文本字符串，x左边，y坐标和可选的最大像素宽度。而且这两个方法都以下列三个属性为基础：  
1. font: 表示文本样式、大小和字体，用CSS中指定字体格式来指定，如："10px Arial"  
2. textAlign: 表示文本对齐方式，"start, end, left, right, center"，建议使用start和end，不要使用left和right  
3. textBaseline: 表示文本的基线。"top, hanging, middle, alphabetic, ideographic, bottom"  
这几个属性都有默认值，不需要重新设置一遍。`fillText()`方法使用fillStyle属性绘制文本。`strokeText()`使用strokeStyle属性为文本描边。相比之下，使用`fillText()`的时候更多，该方法模拟了在网页中正常显示文本。
