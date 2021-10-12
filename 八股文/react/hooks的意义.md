

## hooks之前

1. 逻辑割裂

   ```
   一个完整时间的业务逻辑要分在不同的生命周期中
   ```

2. 逻辑复用困难

   ```
   只能组件层面进行服用
   ```

为了解决上述问题：

1. high order component
2. render props

功能复杂后的问题：wrapper hell

## hooks需要满足

1. 组件state

   ```
   useState 
   // Effect 函数要么没有返回值，要么返回一个 Cleanup 函数。
   ```

   

2. 生命周期

   ```
   useEffect
   
   ```

   

3. 逻辑复用困难



## 参考资料

1. https://segmentfault.com/a/1190000023292296