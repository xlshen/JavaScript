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
2D上下文的两种基本绘图操作是填充和描边。填充就是用指定的样式填充图形；描边就是只在图形的边缘画线。操作的结果取决于两个属性：fillStyle和strokeStyle。 这两个属性值可以是`字符串`、`渐变对象`或`模式对象`，而且它们默认值都是"#000000"。如果为它们指定表示颜色的字符串，可以使用CSS中指定颜色值的任何格式
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
这几个属性都有默认值，不需要重新设置一遍。`fillText()`方法使用`fillStyle`属性绘制文本。`strokeText()`使用`strokeStyle`属性为文本描边。相比之下，使用`fillText()`的时候更多，该方法模拟了在网页中正常显示文本。
```javascript
context.font = "bold 14px Arial";
context.textAlign = "center";
context.textBaseline = "middle";
context.fillText("12", 100, 20);
```
由于绘制文本比较复杂，2D上下文提供了辅助确定文本大小的方法`measureText()`。该方法接收一个参数，要绘制的文本；返回一个TextMetrics对象。返回对象目前只有一个width属性。  
measureText()方法利用font, textAlign和textBaseline的当前值计算文本大小。
```javascript
var fontSize = 100;
context.font = fontSize + "px Arial";
while(context.measureText("Hello world!").width > 140){
 fontSize --;
 context.font = fontSize + "px Arial";
}
context.fillText("Hello world!", 10, 10);
context.fillText("Font size is " + fontSize + "px", 10, 50);
```
###### 变换
通过上下文的变换，可以把处理后的图像绘制到画布上。可以通过以下方法修改变换矩阵：    
1. rotate(angle): 围绕远点旋转图像angle弧度    
2. scale(scaleX, scaleY): 缩放图像，在x方向乘以scaleX，在y方向乘以scaleY。默认值都是1.0    
3. translate(x, y): 将坐标原点移动到(x, y)。执行之后坐标(0, 0)会变成之前由(x, y)表示的点    
4. transform(m1_1, m1_2, m2_1, m2_2, dx, dy): 直接修改变化矩阵，方式乘以以下矩阵:  
m1_1    m2_2    dx  
m2_1    m2_2    dy  
0       0       1  
5. setTransform(m1_1, m1_2, m2_1, m2_2, dx, dy): 将变换矩阵重置为默认状态，然后调用transform()  
```javascript
context.beginPath();
context.arc(100, 100, 99, 0, 2*Math.PI, false);
context.moveTo(194, 100);
context.arc(100, 100, 94, 0, 2*Math.PI, false);
context.translate(100, 100); // 变换原点
context.rotate(1); // 上下文旋转1弧度
context.moveTo(0, 0);
context.lineTo(0, -85);
context.moveTo(0, 0);
context.lineTo(-65, 0);
context.stroke();
```
无论是执行变换还是`fillStyle`，`strokeStyle`等属性，都会在当前上下文中一直有效，除非再做什么修改。虽然没有办法把上下文重置，但有两个方法可以追踪上下文状态变化。如果你知道将来还要返回某组属性与变换的组合，可以调用`save()`方法。调用这个方法后，当时的所有设置都会进入一个栈结构保存，然后可以对上下文进行修改，等想要回到之前保存的设置时，可以调用`restore()`方法，再保存设置的栈结构中往前返回一级，恢复之前状态。连续调用`save()`可以把更多设置保存到栈结构，之后连续调用`restore()`可以一级一级返回。  
```javascript
context.fillStyle = "#f00";
context.save();
context.fillStyle = "#0f0";
context.translate(100, 100);
context.save();
context.fillStyle = "#00f";
context.fillRect(0, 0, 100, 200); // 从(100, 100)开始绘制蓝色矩形
context.restore();
context.fillRect(10, 10, 100, 200); // 从(110, 110)开始绘制绿色矩形
context.restore();
context.fillRect(0, 0, 100, 200); // 从(0, 0)开始绘制红色矩形
```
###### 绘制图像
`drawImage()`方法可以把一幅图像绘制到画布上，可以使用三种不同的参数组合:  
```javascript
var image = document.images[0];
context.drawImage(image, 10, 10); // 绘制图像的x和y坐标
context.drawImage(iamge, 50, 10, 20, 30); // 图像变为20*30像素
```
除了以上两种方式，还可以把图像中的某个区域绘制到上下文中。需要传入9个参数：绘制的图像，源图像x坐标，源图像y坐标，源图像宽度，源图像高度，目标图像的x坐标，目标图像y坐标，目标图像宽度，目标图像高度  
```javascript
// 原始图像的一部分起点(0, 10)，宽和高都是50像素。最终绘制到上下文图像起点(0, 100)，大小为40*60像素
context.drawImage(image, 0, 10, 50, 50, 0, 100, 40, 60); 
```
除了给`drawImage()`方法传入HTML`<img>`元素外，还可以传入另一个`<canvas>`元素作为第一个参数。
###### 阴影
2D上下文会根据下面几个属性值，自动为形状或者路径绘制阴影:  
1. shadowColor: 用CSS颜色格式表示阴影颜色，默认黑色  
2. shadowOffsetX: 形状或路径x轴方向的阴影偏移量，默认0  
3. shadowOffsetY: 形状或路径y轴方向的阴影偏移量，默认0  
4. shodowBlur: 模糊的像素数，默认0，即不模糊  
```javascript
context.shadowOffsetX = 5;
context.shadowOffsetY = 5;
context.shadowBlur = 4;
context.shadowColor = rgba(0, 0, 0, 0.5);
context.fillStyle = "f00";
context.fillRect(10, 10, 50, 50);
context.fillStyle = "rgba(0, 0, 255, 1)";
context.fillRect(30, 30, 50, 50);
```
###### 渐变
渐变由CanvasGradient实例表示，很容易通过2D上下文创建和修改。要创建`线性渐变`，可以调用`createLinearGradient()`方法。该方法接收4个参数：起点x坐标，起点y坐标，终点x坐标，终点y坐标。调用后会创建一个指定大小的渐变，并返回CanvasGradient对象的实例。  
创建了渐变对象后，下一步就是使用`addColorStop()`方法指定色标。接收两个参数：色标位置和CSS颜色值。色标位置是一个0(开始颜色)到1(结束颜色)之间的数字。
```javascript
var gradient = context.createLinearGradient(20, 30, 70, 70);
gradient.addColorStop(0, "white");
gradient.addColorStop(1, "black"); 
context.fillStyle = "#f00";
context.fillRect(10, 10, 50, 50);
context.fillStyle = gradient;
context.fillRect(30, 30, 50, 50);
```
要创建径向渐变（或放射渐变），可以使用`createRadialGradient()`方法。该方法接收6个参数，对应两个圆的圆心和半径。前三个参数指定的是起点圆的圆心(x, y)及半径，后三个参数指定的是终点圆的圆心(x, y)及半径。  
```javascript
var gradient = context.createRadialGradient(55, 55, 10, 55, 55, 30);
gradient.addColorStop(0, "white");
gradient.addColorStop(1, "black");
context.fillStyle = "#f00";
context.fillRect(10, 10, 50, 50);
context.fillStyle = gradient;
context.fillRect(30, 30, 50, 50);
```
###### 模式
模式其实就是重复的图像，可以用来填充或描边图形。调用`createPattern()`方法并传入两个参数:
