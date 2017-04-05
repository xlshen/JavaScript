#### 模块模式
##### 单例模式
```javascript
var foo = (function module(id){
    var sth = "MM";
    var array = [0, 1, 2];
    function doSth(){
        console.log(sth);
    }
    function doArray(){
        console.log(array.join("&"));
    }
    return {
        doSth: doSth,
        doArray: doArray
    };
})("xlshen");
foo.doSth(); // MM
foo.doArray(); // 0&1&2
```
##### 现代模块模式
```javascript
var Modules = (function Manager(){
    var modules = {};

    function define(name, deps, impl){
        for(var i = 0; i < deps.length; i++){
            deps[i] = modules[deps[i]];
        }
        modules[name] = impl.apply(impl, deps);
    }
    function get(name){
        return modules[name];
    }
    return {
        define: define,
        get: get
    };
})();
Modules.define("hello", [], function () {
    var hello = "Hello";
    function sayHello(){
        console.log(sayHello);
    }
    return {
        sayHello: sayHello
    };
});
Modules.define("World", )
```
