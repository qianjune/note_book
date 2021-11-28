ioc 控制反转

是抽象，模糊的概念

DI是IOC的实现

```
没有容器前，主控类是使用其他类实例的类，有了容器，主控类就变成了容器本身，这就是控制反转
```

```
重点在于控制权，业务变化的时候会引起控制代码的改变，IOC的实现就是为了，尽量让控制代码处于OCP的情况，程序员不再去控制（因为业务变化而改变控制代码），而是产品经理去控制（新增业务代码不算影响控制代码）
程序员生产类，具体组装由产品决定，类似乐高
```



容器，主动自动帮我们实例化对象，要使用对象，直接从容器中取，使类互相之间松耦合，没什么互相的依赖

DI（Denpendency Injection） 依赖注入

自动注入可能需要用到的对象（依赖）

```java
public class A{
  public A(IC ic){
    2. 构造注入
  }
  private IC ic;
  public void setIc(IC ic){
    1. 属性注入
    this.ic=ic
  }
}

// 3. 接口注入

public class Container{
  public void getBean(){
    IC ic = new C();
    A a = new A(ic); // 构造注入
    A a1 = new A();
    a1.setIc(ic); // 属性注入
  }
  // 容器 装配这些类
}
```



dip（denpendency inversion principle） 依赖倒置

```
高层模块（高层）不应该依赖底层模块（具体实现），两者都应该依赖抽象
抽象不应该依赖细节
细节应该依赖抽象
```



