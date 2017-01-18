#### 元素大小  
##### 偏移量
> 首先可见大小有其高度、宽度决定，包括所有的内边框、滚动条和边框大小【不包括外边框】  
1. offsetHeight: 元素垂直方向占用的空间，包括元素高度、水平滚动条高度、上边框和下边框宽度  
2. offsetWidth: 元素水平方向占用的空间，包括元素的宽度、垂直滚动条宽度、左右边框的宽度  
3. offsetLeft: 元素左边框到包含该元素的左内边框之间的像素距离  
4. offsetTop: 元素上边框到包含该元素的上内边框之间的像素距离  

其中offsetLeft和offsetTop和包含元素有关，包含元素引用保存在offsetParent属性中，offsetParent属性不一定与parentNode的值相等。
##### 客户区大小
> 元素内容及其内边距所占据的空间大小  
1. clientWidth: 内容区宽度加左右内边距宽度    
2. clientHeight: 内容区高度加上下内边距高度    
[注：滚动条不计算在内]

##### 滚动大小
> 1. scrollHeight: 在没有滚动条的前提下，元素内容区的总高度  
2. scrollWidth: 在没有滚动条的前提下，元素内容区的总宽度  
3. scrollLeft: 被隐藏在内容区域左侧的像素数   
4. scrollTop: 被隐藏在内容区域上方的像素数  

##### 确定元素大小
> getBoundingClientRect()方法，这个方法返回一个矩形对象，包含4个属性：left,top,right,bottom

