## [observable create](https://juejin.cn/post/6921473823543918600#heading-4)

```typescript
// 返回一个指向一个Proxy的变量
const counter = observable({num:0})

 
```



```typescript
// src/observable.js

// proxyToRaw和rawToProxy都是weakMap
// raw代表原始对象，proxy代表包装后的Proxy对象
export function observable (obj = {}) {
  // 如果obj已经是一个observable对象或者不应该被包装，则直接返回它
  if (proxyToRaw.has(obj) || !builtIns.shouldInstrument(obj)) {
    return obj
  }

  // 如果obj已经有一个对应的observable对象，则将其返回。否则创建一个新的observable对象
  return rawToProxy.get(obj) || createObservable(obj)
  // 首次创建会走到 createObservable
}

```



#### shouldInstrument

```typescript
// src/builtIns/index.js
export function shouldInstrument ({ constructor }) {
  // 判断是否是内置对象
  const isBuiltIn =
    typeof constructor === 'function' &&
    constructor.name in globalObj &&
    globalObj[constructor.name] === constructor
  return !isBuiltIn || handlers.has(constructor)
}

```



#### handlers

```typescript
// src/builtIns/index.js
const handlers = new Map([
  [Map, collectionHandlers],
  [Set, collectionHandlers],
  [WeakMap, collectionHandlers],
  [WeakSet, collectionHandlers],
  [Object, false],
  [Array, false],
  [Int8Array, false],
  [Uint8Array, false],
  // 省略部分代码
  [Float64Array, false]
])

```



### createObservable （创建一个新的observable）

```typescript
function createObservable (obj) {
  const handlers = builtIns.getHandlers(obj) || baseHandlers
  const observable = new Proxy(obj, handlers)
  // 保存raw => proxy，proxy => raw 之间的映射关系
  rawToProxy.set(obj, observable)
  proxyToRaw.set(observable, obj)
  storeObservable(obj)
  return observable
}

```

> baseHandlers
>
> ```typescript
> export default { get, has, ownKeys, set, deleteProperty }
> 
> ```
>
> storeObservable
>
> ```typescript
> // src/store.js
> const connectionStore = new WeakMap()
> 
> export function storeObservable (obj) {
>   // 用于后续保存obj.key -> reaction之间映射关系
>   connectionStore.set(obj, new Map())
> }
> 
> ```
>
> 
>
> 

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/610be58ee3dd430d8482fb682509878f~tplv-k3u1fbpfcp-watermark.awebp)



## observe create

```typescript
const counterLogger = observe(()=>console.log(counter.num))
```



```typescript
// src/observer.js
export function observe (fn, options = {}) {
  // const IS_REACTION = Symbol('is reaction')
  // 判断是不是reaction函数
  // 是的话直接执行
  // 不是的话，对fn进行包装处理
  const reaction = fn[IS_REACTION]
    ? fn
    : function reaction () {
      return runAsReaction(reaction, fn, this, arguments)
    }
  // 省略部分代码
  reaction[IS_REACTION] = true
  // 如果非lazy，则直接运行
  if (!options.lazy) {
    reaction()
  }
  return reaction
}

```

> runAsReaction
>
> ```typescript
> // src/reactionRunner.js
> const reactionStack = []
> 
> export function runAsReaction (reaction, fn, context, args) {
>   // 省略部分代码
>   if (reactionStack.indexOf(reaction) === -1) {
>     // 释放(obj -> key -> reactions) 链接并复位清理器链接
>     releaseReaction(reaction)
> 
>     try {
>       // 压入到reactionStack堆栈中，以便于在get陷阱中能建立(observable.prop -> reaction)之间的联系
>       reactionStack.push(reaction)
>       return Reflect.apply(fn, context, args)
>     } finally {
>       // 从reactionStack堆栈中，移除已执行的reaction函数
>       reactionStack.pop()
>     }
>   }
> }
> 
> ```
>
> baseHandler.get
>
> ```typescript
> // src/handlers.js
> function get (target, key, receiver) {
>   const result = Reflect.get(target, key, receiver)
>   // 注册并保存(observable.prop -> runningReaction)
> // 其实就是为对象的指定属性，添加对应的观察者
>   registerRunningReactionForOperation({ target, key, receiver, type: 'get' })
>   const observableResult = rawToProxy.get(result)
>   if (hasRunningReaction() && typeof result === 'object' && result !== null) {
>     // 省略部分代码
>   }
>   return observableResult || result
> }
> 
> ```
>
> > registerRunningReactionForOperation
> >
> > ```typescript
> > // src/reactionRunner.js
> > export function registerRunningReactionForOperation (operation) {
> >   // 从栈顶获取当前正在执行的reaction
> >   const runningReaction = reactionStack[reactionStack.length - 1]
> >   if (runningReaction) {
> >     debugOperation(runningReaction, operation)
> > // 为当前操作注册reaction函数
> >     registerReactionForOperation(runningReaction, operation)
> >   }
> > }
> > 
> > ```
> >
> > > registerReactionForOperation
> > >
> > > ```typescript
> > > // src/store.js
> > > export function registerReactionForOperation (reaction, { target, key, type }) {
> > >   // 省略部分代码
> > >   const reactionsForObj = connectionStore.get(target) // A
> > >   let reactionsForKey = reactionsForObj.get(key) // B
> > >   if (!reactionsForKey) { // C
> > >     reactionsForKey = new Set()
> > >     reactionsForObj.set(key, reactionsForKey)
> > >   }
> > >   if (!reactionsForKey.has(reaction)) { // D
> > >     reactionsForKey.add(reaction)
> > >     reaction.cleaners.push(reactionsForKey)
> > >   }
> > > }
> > > 
> > > ```
> > >
> > > 

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c2f2ef42fde244c19d53351c2aa841ac~tplv-k3u1fbpfcp-watermark.awebp)

## num++

```typescript
counter.num++
```

> 触发set
>
> ```typescript
> // src/handlers.js
> function set (target, key, value, receiver) {
>   // 省略部分代码
>   const hadKey = hasOwnProperty.call(target, key)
>   const oldValue = target[key]
>   const result = Reflect.set(target, key, value, receiver)
>   if (!hadKey) {
>     queueReactionsForOperation({ target, key, value, receiver, type: 'add' })
>   } else if (value !== oldValue) {
>     queueReactionsForOperation({
>       target,
>       key,
>       value,
>       oldValue,
>       receiver,
>       type: 'set'
>     })
>   }
>   return result
> }
> 
> ```
>
> > queueReactionsForOperation
> >
> > ```typescript
> > // src/reactionRunner.js
> > export function queueReactionsForOperation (operation) {
> >   // iterate and queue every reaction, which is triggered by obj.key mutation
> >   getReactionsForOperation(operation).forEach(queueReaction, operation)
> > }
> > 
> > ```
> >
> > > getReactionsForOperation
> > >
> > > ```typescript
> > > // src/store.js
> > > export function getReactionsForOperation ({ target, key, type }) {
> > >   const reactionsForTarget = connectionStore.get(target)
> > >   const reactionsForKey = new Set()
> > > 
> > >   if (type === 'clear') {
> > >     reactionsForTarget.forEach((_, key) => {
> > >       addReactionsForKey(reactionsForKey, reactionsForTarget, key)
> > >     })
> > >   } else {
> > >     addReactionsForKey(reactionsForKey, reactionsForTarget, key)
> > >   }
> > > 	// 省略部分代码
> > >   return reactionsForKey
> > > }
> > > 
> > > ```
> > >
> > > queueReaction
> > >
> > > ```typescript
> > > // src/reactionRunner.js
> > > function queueReaction (reaction) {
> > >   debugOperation(reaction, this)
> > >   // queue the reaction for later execution or run it immediately
> > >   if (typeof reaction.scheduler === 'function') {
> > >     reaction.scheduler(reaction)
> > >   } else if (typeof reaction.scheduler === 'object') {
> > >     reaction.scheduler.add(reaction)
> > >   } else {
> > >     // 没有配置scheduler，会直接到这里
> > >     reaction()
> > >   }
> > > }
> > > 
> > > ```
> > >
> > > 