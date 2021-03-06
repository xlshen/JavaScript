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
```html
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
[//检测元素是否有指定属性：`hasAttribute()`如果存在返回true，否则false]
上面三个属性都可以用`getAttribute()`获取，通过`getAttribute()`还可以获取自定义的特性的值。
```html
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
`setAttribute('attr', 'value')`通过该方法设置的特性名会统一转换成小写形似，即`"ID"`最终变为`"id"`，因为所有的特性都是属性，所以直接给属性赋值可以设置特性的值，为元素添加一个自定义属性，该属性不会自动称为元素的特性。  
`removeAttribute()`这个方法用来彻底删除元素的特性。该方法不仅可以删除特性的值，而且还会从元素中完全删除特性。
#### attributes属性
`Element`类型是使用`attributes`属性的唯一一个`DOM`节点类型。`attributes`属性中包含一个`NamedNodeMap`，与`NodeList`类似也是动态集合。元素每个特性都由一个`Attr`节点表示，每个节点都保存在`NamedNodeMap`对象中。NamedNodeMap对象拥有下列方法：  
1. `getNamedItem(name)`: 返回`nodeName`属性等于`name`的节点  
2. `removeNamedItem(name)`：移除`nodeName`属性等于name的节点  
3. `setNamedItem(node)`：向列表中添加节点，以节点的`nodeName`属性为索引  
4. `item(pos)`：返回位于数字`pos`位置处的节点  
```javascript
// 以下两种获取元素特性方法等同
var id = element.attributes.getNamedItem("id").nodeValue;
var id = element.attributes["id"].nodeValue;
// 设置元素特性值
element.attributes["id"].nodeValue = "example";
// 删除给定名称特性
var oldAttr = element.attributes["id"].removeNamedItem("id");
// 该返回删除的特性节点【和removeAttribute()唯一区别】
```
#### 创建元素
使用`document.createElement(tagName)`方法创建新元素，`tagName`为元素标签名，创建了新元素的同时也为其设置了ownerDocument属性。
```javascript
var div = docuemnt.createElement("div");
div.id = "myDiv";
div.className = "example";
```
