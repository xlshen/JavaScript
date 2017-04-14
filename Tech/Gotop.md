#### 缓慢Gotop实现
```javascript
window.onload = function(){
    // requestAnimationFrame实现版本，存在兼容问题，注意安全！
    // window.requestAnimationFrame = window.requestAnimationFrame || window.mozRequestAnimationFrame || window.webkitRequestAnimationFrame || window.msRequestAnimationFrame;
   var obtn = document.getElementById('gotop');
   var clientHeight = document.body.clientHeight || document.documentElement.clientHeight; //获取可视区域的高度
   var timer = null; //定义一个定时器
   var isTop = false; //定义一个布尔值，用于判断是否到达顶部

   window.onscroll = function(event) { //滚动条滚动事件

       //获取滚动条的滚动高度
       var osTop = document.documentElement.scrollHeight || document.body.scrollHeight;

       if (osTop >= clientHeight) { //如果滚动高度大于可视区域高度，则显示回到顶部按钮
           obtn.style.display = 'block';
       } else { //否则隐藏
           obtn.style.display = 'none';
       }
       //主要用于判断当 点击回到顶部按钮后 滚动条在回滚过程中，若手动滚动滚动条，则清除定时器
   }

   obtn.onclick = function() { //回到顶部按钮点击事件
       //设置一个定时器 requestAnimationFrame实现版本
    //    (function step(timestamp) {
    //        //获取滚动条的滚动高度
    //        var osTop = document.documentElement.scrollTop || document.body.scrollTop;
    //        //用于设置速度差，产生缓动的效果
    //        var speed = Math.floor(-osTop / 8);
    //        document.documentElement.scrollTop = document.body.scrollTop = osTop + speed;
    //        timer = requestAnimationFrame(step);
    //        isTop = true; //用于阻止滚动事件清除定时器
    //        if (osTop == 0) {
    //            window.cancelAnimationFrame(timer);
    //        }
    //    })();

       // 设置定时器，setInterval实现版本
       timer = setInterval(function gotop(){
           //获取滚动条的滚动高度
           var osTop = document.documentElement.scrollTop || document.body.scrollTop;
           //用于设置速度差，产生缓动的效果
           var speed = Math.floor(-osTop / 8); // 8可以虚化称为动态参数，值越大滚动越缓慢
           document.documentElement.scrollTop = document.body.scrollTop = osTop + speed;
           isTop = true; //用于阻止滚动事件清除定时器
           if (osTop == 0) {
               clearInterval(timer);
           }
       }, 30)
   }
   // 滚动事件兼容
   var support = "onwheel" in document.createElement("div") ? "wheel" : // Modern browsers support "wheel"
              document.onmousewheel !== undefined ? "mousewheel" : // Webkit and IE support at least "mousewheel"
              "DOMMouseScroll";
   switch (support) {
       case "wheel":
           window.onwheel = function(event) {
               if (event.deltaY > 0 && isTop) {
                   // window.cancelAnimationFrame(timer);
                   clearInterval(timer);
               }
           };
           break;
       case "mousewheel":
           window.onmousewheel = function(event){
               if(event.wheelDelta < 0){
                   // window.cancelAnimationFrame(timer);
                   clearInterval(timer);
               }
           }
       default:
           window.addEventListener("DOMMouseScroll", function(event) {
               if(event.detail > 0){ // detail > 0向下滚动，反之向上
                   // window.cancelAnimationFrame(timer);
                   clearInterval(timer);
               }
           });
   }
}
```
