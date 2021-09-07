## new [function]



**proto** 是原型,prototype是函数默认的一个属性,它指向一个对象,这个对象的constructor属性指向函数本身.

```js
// mdn
function Foo(){}
var o = new Object();
o.[[Prototype]] = Foo.prototype;
Foo.call(o);
```



```js
// 等价
let fun = new Object();
fun.__proto__ = TestFun.prototype;
TestFun.call(fun)

 TestFun.prototype === {
		constructor:{
      arguments: // 参数
      length：// 参数数量
      caller：// 类似函数相互调用后的链接关系
      name：//函数名
		}
 }
```

