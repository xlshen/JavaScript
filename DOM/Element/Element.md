### Element类型
#### 特征
1. `nodeType`值为1  
2. `nodeName`值为标签名  //`tagName`返回和`nodeName`相同，`tagName`容易理解而已【都是大写的标签名】
3. `nodeValue`值为`null`  

#### HTML元素
`HTML`元素都是由`HTMLElement`类型表示，`HTMLElement`除了继承自`Element`并添加了一些属性。  
1. `id`，元素在文档中的唯一标识  
2. `title`，有关元素的附加信息  
3. `className`，与元素`class`对应  
例如：
```javascript
<div id="myDiv" class="bd" title="body"></div>
<script>
var dir = document.getElementById('myDiv');
alert(dir.id); // "myDiv"
alert(dir.className); // "bd"
alert(dir.title); // "body"
// 可以利用该属性更改元素属性
div.id = "yourDiv";
div.className = "yd";
div.title = "footer";
</script>
```
#### 取得特性
操作特性的`DOM`方法有三个：`getAttribute()`、`setAttribute()`和`removeAttribute()`
上面三个属性都可以用`getAttribute()`获取，通过`getAttribute()`还可以获取自定义的特性的值。
```javascript
<div id="myDiv" data-special="world"></div>
<script>
//根据html5规范，自定义特性应该加上data-前缀以便验证
var val = document.getElementById('myDiv').getAttribute('data-special'); 
</script>
```
有两类特殊的特性，它们虽然有对应的属性名，但属性值与通过`getAttribute()`返回的值不同。  
1. `style`特性  
通过`getAttribute()`访问时返回的`style`特性值包含的是`CSS`文本，而通过属性来访问则返回一个对象。  
2. `onclick`特性  
通过`getAttribute()`访问时返回相应代码的字符串。而访问`onclick`属性是，则会返回一个`JavaScript`函数（未指定返回`null`）
#### 设置特性
`setAttribute('attr', 'value')`通过该方法设置的特性名会统一转换成小写形似，即`"ID"`最终变为`"id"`，为元素添加一个自定义属性，该属性不会自动称为元素的特性。  
`removeAttribute()`这个方法用来彻底删除元素的特性。该方法不仅可以删除特性的值，而且还会从元素中完全删除特性。
