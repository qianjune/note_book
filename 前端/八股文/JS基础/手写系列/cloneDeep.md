手写系列 - cloneDeep

```typescript
const deepTag = ['[object Array]','[object Object]','[object Map]','[object Set]','[object Arguments]']
function isObject(target){
  const type = typeof target
  if(target!==null&&type!=='function'&&type!=='object'){
    return false
  }
  return true
}
function cloneDeep(target){
  if(!isObject(target)){
    return target
  }
  const type = Object.toString.call(target)
  let cloneTarget
  if(deepTag.includes(type)){
    // 需要循环
    const Constr = target.constructor
    cloneTarget = new Constr()
  }else{
     const Constr = target.constructor
    // 不需要循环
    if(type=bool|number|string|error|date){
      return new Constr(target)
    }else if(type===regexp){
      const result = new tareget.constructor(taeget.source)
      result.lastIndex = target.lastIndex
      return result
    }else if(type===symbol){
      return Object(Symbol.prototype.valueOf.call(target))
    }else if(type===func){
      const funcString = func.toString()
      // 匹配出param
      // 匹配出body
      if(param){
        const paramArr = params[0].split(',')
        return new Function(...paramArr,body[0])
      }else{
        return new Function(body[0])
      }
    }
    return null
  }
  
  // 克隆map
  // 克隆set
  // 克隆object或array
  
}
```





## 参考资料

1. https://github.com/ConardLi/ConardLi.github.io/blob/master/demo/deepClone/src/clone_6.js
2. https://segmentfault.com/a/1190000020255831

