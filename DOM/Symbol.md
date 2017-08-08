#### Symbol到底是用来干嘛的？！
Symbol之前，如果定义了一个对象，里面含有一个属性name
```js
  var obj = {
    name: "xlshen"
  };
```
此时，如果另一个地方也用到该对象，同名的属性，并且恰巧修改了该值就会出现问题了，其实本意不是修改之前的值，只不过意思相同的属性名；但如果我定义了一个类型代表该值，此值唯一，只是一个代表name意思的唯一值，就不存在这个问题。因为字符串属性名和Symbol属性名访问方式不同，不可能存在冲突
```js
  var symbol_name = Symbol("name");
  var obj = {
    symbol_name: "name", // 字符串属性名
    [symbol_name]: "xlshen" // Symbol属性名
  };
  obj.symbol_name || obj["symbol_name"] // 字符串属性访问方式
  obj[symbol_name] // Symbol属性访问
```
由于每一个 Symbol 值都是不相等的，这意味着 Symbol 值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。这对于一个对象由多个模块构成的情况非常有用，能防止某一个键被不小心改写或覆盖
```js
  var xlshen_symbol = Symbol("xlshen");
  let xlshen_obj = {
      first: "xlshen",
      last: "xlshen",
      [xlshen_symbol]: "xlshen"

  };
  function xlshen_re(){
      let xlshen_symbol = Symbol("xl");
      return {
          [xlshen_symbol]: "name"
      }
  }
  xlshen_obj // first:"xlshen" last:"xlshen" Symbol(xl):"name" Symbol(xlshen):"xlshen"
```
