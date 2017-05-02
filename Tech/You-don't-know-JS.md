#### P225 Trick
```js
/**
 * [ins description]
 * 封装成函数，通过该函数可以向原始函数中传递初始化对象并传递初始化参数
 * @type {Object}
 */
 var ins = new (Function.prototype.bind.apply(fn, [null, arg1, arg2, ...]));
 var ins = new (Function.prototype.bind.call(fn, null, arg1, arg2, ...));
 ins instanceof fn; // true
 // 封装成新函数
 function foo(fn){
     return new new (Function.prototype.bind.apply(fn, arguments);
 }
 var ins = foo(funs, 1, 2, 3);

 /**
  * [shrink description]
  * 返回函数，通过该函数可以直接传递参数调用
  * @type {Function}
  */
var shrink = Function.prototype.apply.bind(fn, null);
shrink([arg1, arg2, ...]);
// 例如
var max = Function.prototype.apply.bind(Math.max, null);
max([1, 3, 5, 0, 2]);
```
