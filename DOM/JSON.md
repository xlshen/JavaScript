#### JSON
`JSON是一种数据结构而非编程语言`  
##### 语法
1. 简单值：使用与JavaScript相同的语法，可以在JSON中表示字符串、数值、布尔型和null。但JSON不支持JavaScript的特殊值undefined // JSON字符串必须用双引号！  
2. 对象：一组无序的键值对 // 对象属性必须加双引号！  
3. 数组：有序的值的列表  
```javascript
// JavaScript
var object = {
  name: "xlshen",
  age: 18
};
// JSON
{
  "name": "xlshen",
  "age": 18
}
```
##### JSON对象
两个方法：`stringify()`和`parse()`

`JSON.stringigy()`:
除了序列化JavaScript对象外，还可以接受另外两个参数：过滤器（数组或函数）和是否结果保留缩紧的选项
1. 过滤结果：
如果过滤器是数组，那么JSON.stringify()结果中只含有数组中列出的属性
```javascript
var book = {
  "title":"Professional JS",
  edition: 3,
  year: 2010
};
var jsonText = JSON.stringify(book, ["title", "edition"]);
// "{"title":"Professional JS","edition":3}"
```
如果第二个参数是一个函数，该函数有两个参数：属性名和属性值。
```javascript
var jsonText = JSON.stringify(book, function(key, value){
  switch(key){
    case "title":
      return "JavaScript";
    case "edition":
      return 1;
    default: 
      return value;
  }
});
// "{"title":"JavaScript","edition":1,"year":2010}"
```
2. 字符串缩进  
JSON.stringify()第三个参数控制结果中字符串缩进和空白符。如果该参数是一个数值，则表示缩进的字符数，如果非数值，则这个字符用作缩进字符（不再使用空格）【最大缩进空格数为10，超过的自动转换为10，字符一样】
```javascript
var jsonText = JSON.stringify(book, null, 4);
// "{
//    "title": "Professional JS",
//    "edition": 3,
//    "year": 2010
//  }"
var jsonText = JSON.stringify(book, null, "- -");
// "{
// - -"title": "Professional JS",
// - -"edition": 3,
// - -"year": 2010
// }"
```
