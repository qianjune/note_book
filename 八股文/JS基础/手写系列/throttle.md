# 手写系列 - throttle



```typescript
// 定时器版
function throttle(fn,wait){
  let timer
  return function(){
    const context = this
    const args = arguments
    if(!timer){
      timer=setTimeout(()=>{
        timer=null
        fn.apply(context,args)
      },wait)
    }
  }
}
```



```typescript
// 时间戳版
function throttle(fn,wait){
  let pre=0
  return function(){
     const context = this
    const args = arguments
    const now = Date.now()
    if(now-prev>wait){
      fn.apply(context,args)
      pre=now
    }
  }
}
```



## 参考资料

1. https://segmentfault.com/a/1190000027085226