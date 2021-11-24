



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

