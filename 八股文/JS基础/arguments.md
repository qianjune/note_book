# [arguments](https://zhuanlan.zhihu.com/p/23007032)

1. 用arguments.length去判断参数数量，从而实现函数重载

2. 参数默认值的设置对arguments没有影响，arguments只显示实际传值

3. .../rest 对arguments试用

4. 用apply(this,arguments)可以传递方法接收到的参数

5. ```text
   // 转数组
   Array.prototype.slice.call(arguments);
   ```

6. arguments不能直接return出去，可以循环生成新数组return
7. 非严格模式可以改值，严格模式不能改

