> DOM1级定义了一个Node接口，该接口将由DOM中所有节点类型实现。这个Node接口在JavaScript中作为Node类型实现；

#### Node类型

##### 属性：  
1. `nodeType`  
2. `nodeName`  
3. `nodeValue`  
4. `childNodes` // `NodeList`对象，可通过`childNodes[i]`或者`childNodes.item(i)`来获取  
5. `nextSibling`  
6. `previousSibling`  
7. `firstChild`  
8. `lastChild`  
9. `ownerDocument`  
10. `parentNode`  

##### 方法：
1. `appendChild()`// 增加节点，返回增加的新节点，如果该节点存在，则该节点从原来位置转移到新位置  
2. `insertBefore(newNode, refNode)`// 返回新节点，如果参照节点为`null`，则`insertBefore()`和`appendChild()`执行相同操作  
3. `replaceChild(newNode, oldNode)`// 替换`oldNode`，返回旧节点  
4. `removeChild()`// 移除节点  
// 上面四个方法操纵的都是元素的子节点，所以首先要获取元素的附加点`node.parentNode.xxx(node, xxx)`;
5. `cloneNode(boollean)`// 拷贝节点，参数true为深复制，复制该元素及其子元素节点，false只复制该元素节点，返回该赋值节点【该方法不会复制节点上的事件处理程序】  
6. `normalize()`// 规范文档树中的文本节点  
```javascript
// 对NodeList对象转换为数组可采用：Array.prototype.slice.call(Node.childNodes); IE8及以前无效
function convertToArray(nodes){
  var array = null;
  try{
    array = Array.prototype.slice.call(nodes.childNodes); // 非IE
  }catch(ex){
    array = new Array();
    for(var i=0, len = nodes.length; i < len; i++){
      array.push(nodes[i]);
    }
    return array;
  }
}
```
