#### 选择API
1. `querySelector()` // 接收一个CSS选择符，返回与该模式匹配的第一个元素，没有返回null；调用可以是document,element,documentFragment
2. `querySelectorAll()` // 接收一个CSS选择符，返回与该模式所有匹配的元素，NodeList实例，如果没有NodeList为空；调用可以是document,element,documentFragment
3. `matchesSelector()` // 接收CSS选择符，如果如调用元素匹配返回true，否则false；暂无浏览器支持改写法（mozMatchesSelector, webkitMatchesSelector, msMatchesSelector）
```javascript
document.body.querySelector("#list");
element.querySelector("#list");
document.body.querySelectorAll(".list");
document.body.webkitMatchesSelector(".bodies");
```
#### 元素遍历  
1. `childElementCount` // 子元素的个数 
2. `firstElementChild` // 第一个子元素
3. `lastElementChild` // 最后一个子元素
4. `previousElementSibling` // 前一个同辈元素
5. `nextElementSibling` // 后一个同辈元素

#### HTML5
1. `getElementsByClassName()` // 返回NodeList
2. `classList属性` // DOMTokenList实例，有length属性，可以使用item()或者[]获取每个元素，此外还有以下方法【Firefox和Chrome支持】
  ① `add(value)` // 将指定的字符串添加到列表中。如果值存在，就不添加
  ② `contains(value)` // 列表中是否存在给定的值，如果存在返回true，否则false
  ③ `remove(value)` // 从列表中删除给定的字符串
  ④ `toggle(value)` // 如果列表中存在给定的值，删除它；如果列表中没有给定的值，添加它。
  ```javascript
  div.classList.add("user");
  div.remove("user");
  div.toggle("user");
  ```
