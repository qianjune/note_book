## Proxy



```typescript
const proxy = new Proxy(target,handler)
```

### 支持的拦截接口

| 接口                                          | 作用                                                         |
| --------------------------------------------- | ------------------------------------------------------------ |
| **get(target, propKey, receiver)**            | 拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`          |
| **set(target, propKey, value, receiver)**     | 拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。 |
| **has(target, propKey)**                      | 拦截`propKey in proxy`的操作，返回一个布尔值。               |
| **apply(target, object, args)**               | 拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。 |
| **deleteProperty(target, propKey)**           | 拦截`delete proxy[propKey]`的操作，返回一个布尔值。          |
| **construct(target, args)**                   | 拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。 |
| **ownKeys(target)**                           | 拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性 |
| **getOwnPropertyDescriptor(target, propKey)** | 拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。 |
| **defineProperty(target, propKey, propDesc)** | 拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。 |
| **setPrototypeOf(target, proto)**             | 拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。 |
| **getPrototypeOf(target)**                    | 拦截`Object.getPrototypeOf(proxy)`，返回一个对象。           |
| **preventExtensions(target)**                 | 拦截`Object.preventExtensions(proxy)`，返回一个布尔值。      |
| **isExtensible(target)**                      | 拦截`Object.isExtensible(proxy)`，返回一个布尔值。           |





## 参考资料

1. https://es6.ruanyifeng.com/#docs/proxy

