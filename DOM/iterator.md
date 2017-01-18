#### 遍历
##### NodeIterator
> document.createNodeIterator()方法创建它的实例   
1. root：搜索根节点    
2. whatToShow: 要访问节点的数字代码（位掩码）    
3. filter: 是一个NodeFilter对象，或者一个表示应该接受还是拒绝的某种特定节点的函数    
4. entityReferenceExpansion: 布尔值，是否扩展实体。HTML中没有用    

其中filter参数来指定NodeFilter对象，或者指定一个功能类似的节点过滤器的函数。每个NodeFilter对象只有一个方法，即acceptNode()；如果应该访问给定的节点返回NodeFilter.FILTER_ACCEPT，如果不应该访问，该方法返回NodeFilter.FILTER_SKIP。由于NodeFilter是一个抽象类型，不能创建实例，因此不能直接创建它的实例，在必要时，只要创建一个包含acceptNode()方法的对象，然后传入createNodeIterator()中即可。
```javascript
var filter = {
  acceptNode: function(node){
    return node.tagName.toLowerCase() === "p" ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP;
  }
}
var iterator = document.createNodeIterator(root, NodeFilter.SHOW_ELEMENT, filter, false);
// 如果不指定root，则第三个参数filter传入null
// var iterator = document.createNodeIterator(document, NodeFilter.SHOW_ALL, null, false);
```
NodeFilterIterator类型的主要两个方法：nextNode()和previousNode()

##### TreeWalker  
TreeWalker除了nextNode()和previousNode()方法之外还有：
+ parentNode(): 遍历到当前节点的父节点
+ firstChild(): 遍历到当前节点的第一个子节点
+ lastChild(): 遍历到当前节点的最后子节点
+ nextSibling(): 遍历到当前节点的下一个同辈节点
+ previousSibling(): 遍历到当前节点的上一个同辈节点
