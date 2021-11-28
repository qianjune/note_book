



## 参考资料

1. https://spring.io/projects/spring-boot#learn spring官网
2. https://blog.csdn.net/jisuanjiguoba/article/details/81532965 依赖注入（DI）和控制反转（IoC）的理解

JAVA是DPL语言

## 包管理

Maven：XML

Gradle：Groovy（DSL语言，类似SQL，解决特定问题的语言）

如果要干预编译过程用Gradle，代码量少，阅读清晰，内部也复用了Maven的xxx，为了兼容



所有软件的复杂性 是为了 可维护代码

包扫描 注解

@Component

@Service

@Repository

@Autowired

单例 多例

bean定义

软件工程的原则

```
1. 开闭原则（ocp）Open Closed principle
软件、函数、类 扩展开发 修改封闭
应用：
api（版本）
```

```
面向抽象编程 才能实现开闭原则
interface abstract （java中）
三大特性 多态性
面向抽象还是具体
```

```
面向抽象编程的发展
step1 interface
step2 设计模式：工厂模式
step3 IOC/DI
=》面向抽象 =》OCP =》可维护的代码
```

```
重要结论：
1.单纯的interface可以统一方法的调用。但是不能统一对抗实例化
2. 面向对象，实例化对象，调用方法。
3. 只有一端代码中没有new的出现，才能保持代码的相对稳定，才能逐步实现OCP
4. 实质是一段代码如果要保持稳定，就不应该负责对象的实例化
5. 对象实例化是不可能消除的
6. 把对象实例化的过程，转移到其他的代码片段里
7. 代码中总是会存在不稳定，隔离这些不稳定，保证其他的代码是稳定的
8. 变化造成了不稳定
9. 配置文件属于外部文件，不属于代码本身，可以理解为用户输入
```

计算机代码

本质是实现现实世界 业务 映射