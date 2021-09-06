## AST

### [acorn](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2Facornjs%2Facorn)（webpack）

```
webpack实现的javascript parser
```

```
流程总结
1. webpack最基本的功能其实是将JS的高级模块化语句，import和require之类的转换为浏览器能认识的普通函数调用语句。
2. 要进行语言代码的转换，我们需要对代码进行解析。
3. 常用的解析手段是AST，也就是将代码转换为抽象语法树。
4. AST是一个描述代码结构的树形数据结构，代码可以转换为AST，AST也可以转换为代码。
5. babel可以将代码转换为AST，但是webpack官方并没有使用babel，而是基于acorn自己实现了一个JavascriptParser。
6. 本文从webpack构建的结果入手，也使用AST自己生成了一个类似的代码。
7. webpack最终生成的代码其实分为动态和固定的两部分，我们将固定的部分写入一个模板，动态的部分在模板里面使用ejs占位。
8. 生成代码动态部分需要借助babel来生成AST，并对其进行修改，最后再使用babel将其生成新的代码。
9. 在生成AST时，我们从配置的入口文件开始，递归的解析所有文件。即解析入口文件的时候，将它的依赖记录下来，入口文件解析完后就去解析他的依赖文件，在解析他的依赖文件时，将依赖的依赖也记录下来，后面继续解析。重复这种步骤，直到所有依赖解析完。
10. 动态代码生成好后，使用ejs将其写入模板，以生成最终的代码。
11. 如果要支持require或者AMD，其实思路是类似的，最终生成的代码也是差不多的，主要的差别在AST解析那一块。
```





### babel

```
@babel/parser        // 将源代码转为 AST
@babel/traverse      // AST 开发的核心，95% 以上的代码量都是通过 @babel/traverse 在写 visitor。
@babel/generator     // 将操作过的 AST 生成对应源代码
@babel/types         // 用于创建 ast 节点，判断 ast 节点，在实际的开发中会经常用到
@babel/template      // 可以创建 ast 节点，但过于繁琐，通过 @babel/template 则可以快速创建整段的 ast 节点
```

@babel/traverse  创建的visitor 的第一个参数是 path

![图片](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naIHzTVz2UnkrzvJvoS4sziaibACriaibss4xBwlxicJjqT4cfmuAN42L22icadhZAzNiarAPSOnFvlJUDEAQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 参考资料

1. https://mp.weixin.qq.com/s/SlXIjnwbHS_enHXlZAnMMw 用JS解释JS！详解AST及其应用

2. [手写一个webpack，看看AST怎么用](https://segmentfault.com/a/1190000039231950)

