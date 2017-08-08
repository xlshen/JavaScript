#### Symbol到底是用来干嘛的？！
Symbol之前，如果定义了一个对象，里面含有一个属性name
```js
  var obj = {
    name: "xlshen"
  };
```
此时，如果另一个地方也用到该对象，同名的属性，并且恰巧修改了该值就会出现问题了，其实本意不是修改之前的值，只不过意思相同的属性名；但如果我定义了一个类型代表该值，此值唯一，只是一个代表name意思的唯一值，就不存在这个问题
```js
  var symbol_name = Symbol("name");
  var obj = {
    [symbol_name]: "xlshen"
  };
```
