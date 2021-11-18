## 装饰器

### 类的装饰器

```typescript
@decorator
class A{}

// 等价于
class A{}
A = decorator(A)||A

// 接受到一个target，代表类本身
// 添加静态属性（A.test）或实例属性（A.propotype.test）
// mixins：把另外的方法混合到实例上
```



### 方法的装饰器

```typescript
// target：类，实例（有实例装饰实例，没实例装饰原型）
// name：方法名
function readonly(target, name, descriptor){
  // descriptor对象原来的值如下
  // {
  //   value: specifiedFunction,  // 被装饰的函数
  //   enumerable: false,     // 是否可遍历
  //   configurable: true,当且仅当该属性的 configurable 键值为 true 时，该属性的描述符才能够被改变，同时该属性也能从对应的对象上被删除。默认为 false。
  //   writable: true
  // };
  descriptor.writable = false;
  return descriptor;
}
```

一般函数不能用装饰器，应为有函数提升

可以用高阶函数的形式，包装一般函数

```typescript
function doSomething(name) {
  console.log('Hello, ' + name);
}

function loggingDecorator(wrapped) {
  return function() {
    console.log('Starting');
    const result = wrapped.apply(this, arguments);
    console.log('Finished');
    return result;
  }
}

const wrapped = loggingDecorator(doSomething);
```



## 参考资料

1. https://es6.ruanyifeng.com/#docs/decorator#%E7%B1%BB%E7%9A%84%E8%A3%85%E9%A5%B0