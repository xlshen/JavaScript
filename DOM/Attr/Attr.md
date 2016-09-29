### Attr类型
> 元素的特性在DOM中以Attr类型表示，技术上讲，特性就是存在元素的attributes属性中的节点。

`Attr`类型特性：  
1. `nodeType = 11`  
2. `nodeName = "特性名称"`  
3. `nodeValue = "特性值"`  
4. `parentNode = null`  

特性一般用`getAttribute()`、`setAttribute()`和`removeAttribute()`方法，很少直接引用特性节点  

`Attr`对象有三个属性：`name`、`value`和`specified`。其中`name`是特性名称（`nodeName`相同），`value`特性值（`nodeValue`相同）,而`specified`是一个布尔值，用以区分特性是代码中指定的还是默认的。
