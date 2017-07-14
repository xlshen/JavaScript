#### 部署pipeline
> 部署管道机制（pipeline），即前一个函数的输出是后一个函数的输入

```js
// 巧妙运用array.reduce()的前一个输入进入回调函数先执行，然后结果作为下一次的输入继续执行
// 第二个参数其实在此是函数，把第一次的结果作为第二次执行的输入进行
const pipeline = (...funcs) => val => funcs.reduce((a, b) => b(a), val);

const plus1 = a => a + 1;
const mult2 = a => a * 2;

const addThenMult = pipeline(plus1, mult2);

addThenMult(5)
```
