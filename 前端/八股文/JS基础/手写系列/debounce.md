## 手写系列 - debounce

```typescript
function debounce(fn,wait,immediate){
	let timer;
  return function(...args){
    let context = this
    // let args = arguments
    timer&&clearTimerout(timer)
    if(immediate&&!timer){
      fn.apply(context,args)
    }
    timer = setTimeout(()=>{
      fn.apply(context,args)
    },wait)
  }
}
```



## 参考资料

1. https://github.com/sisterAn/JavaScript-Algorithms/issues/95