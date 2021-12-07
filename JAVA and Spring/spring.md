SSM Spring + Spring MVC + MyBatis

Spring Framework

```
Spring Boot 是 对Spring Framework的一中更好用的封装（xml换成注解）

优势：自动配置 有什么用

类比Java

TP、Flask、KOA

优势是什么好处是什么
```

OCP->IOC

IOC实现：容器 加入容器 注入

目的：

抽象意义：控制权交给客户

# 加入容器方式：

### xml

### 注解

#### stereotype annotations 模式注解

记入容器

@Component（组件/类/bean 加入容器）

```
因为实例化和注入过程是自动的，无法解决构造时传值，对类属性进行赋值的工作
```



---------定义功能意义----------------------

@Service

@Controller

@Repository（意义 仓储，数据库模型）



@Configuration

可以控制实例化过程

用来代替Spring的xml配置方式

（@RestController）= @Controller + @Repository

都是以@Component为基础





```
1. @Autowired 注入（IOC对象实例化，注入的时机-启动时实例化）
 默认是立即/提前 实例化
 可以通过方法延迟实例化 @Lazy（在访问时实例化） 在类上（依赖这个类的类也需要延迟，不然无效）
属性
required 是否允许空值
私有成员变量注入不推荐
## 注入方式
### byType 取名iSkill（按接口名取名），如果有多个实现同一个接口的bean，就无法判断用哪个
（1. 找不大任何，报错 2.找到一个，直接注入 3. 找到多个，不一定报错，会按字段名字推测是哪个bean）
### byName 根据名字去找bean
可以通过@Qualifier（bean的名字）指定注入的bean（主动注入）
-----------
2.构造函数形式注入（推荐）
3. setter注入
```

### 应对变化的策略：

1.定义一个interface，用多个类是实现这个interface（策略模式）@用条件注解

```
1. byname 切换bean name
2. @Qulifier 指定bean
3. 有选择的只注入一个bean 注释掉某个bean上的@Component注解
4. 使用@Primary 提交优先级
5。 条件注解@Conditional 可以自定义条件注解
	@Conditional + condition
```



2. 一个类，通过更改类的属性，来达到变化（通过配置去改变，配置隔离）（防御性编程，一个类也实现一个接口）---用@Configuration（是一种编程模式）和@Bean（将类和配置文件绑定在一定，并且把ioc注入容器的模式）