# 一. Spring的常用注解

## 1. 用于注册bean对象注解

### 1.1. @Component

**作用：**

```
调用无参构造创建一个bean对象，并把对象存入spring的IOC容器，交由spring容器进行管理。相当于在xml中配置一个bean。
```

**属性：**

```
value：指定bean的id。如果不指定value属性，默认bean的id是当前类的类名。首字母小写。
```

### 1.2. @Controller

**作用：**

```
作用上与@Component。一般用于表现层的注解。 
```

**属性：**

```
value：指定bean的id。如果不指定value属性，默认bean的id是当前类的类名。首字母小写。
```

### 1.3. @Service

**作用：**

```
作用上与@Component。一般用于业务层的注解。
```

**属性：**

```
value：指定bean的id。如果不指定value属性，默认bean的id是当前类的类名。首字母小写。
```

### 1.4. @Repository

**作用：**

```
作用上与@Component。一般用于持久层的注解。
```

**属性：**

```
value：指定bean的id。如果不指定value属性，默认bean的id是当前类的类名。首字母小写。
```

### 1.5. @Bean

**作用：**

```
用于把当前方法的返回值作为bean对象存入spring的ioc容器中
```

**属性：**

```
name：用于指定bean的id。当不写时，默认值是当前方法的名称。注意：当我们使用注解配置方法时，如果方法有参数，spring框架会去容器中查找有没有可用的bean对象，查找的方式和Autowired注解的作用是一样的。
```

**案例：**

```java
/**
 * 获取DataSource对象
 * @return
 */
@Bean(value = "dataSource")
public DataSource getDataSource() {
    try {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass(this.driver);
        dataSource.setJdbcUrl(this.url);
        dataSource.setUser(this.username);
        dataSource.setPassword(this.password);
        return dataSource;
    }catch (Exception exception) {
        throw new RuntimeException(exception);
    }
}
```

## 2. 用于依赖注入的注解

### 2.1. @Autowired

**作用：**

```
@Autowire和@Resource都是Spring支持的注解形式动态装配bean的方式。Autowire默认按照类型(byType)装配，如果想要按照名称(byName)装配，需结合@Qualifier注解使用。
```

**属性：**

```
required：@Autowire注解默认情况下要求依赖对象必须存在。如果不存在，则在注入的时候会抛出异常。如果允许依赖对象为null，需设置required属性为false。
```

**案例：**

```java
@Autowire 
@Qualifier("userService") 
private UserService userService;
```

### 2.2. @Qualifier

**作用：**

```
在自动按照类型注入的基础之上，再按照 Bean 的 id 注入。它在给字段注入时不能独立使用，必须和 @Autowire一起使用；但是给方法参数注入时，可以独立使用。
```

**属性：**

```
value：用于指定要注入的bean的id，其中，该属性可以省略不写。
```

**案例：**

```java
@Autowire
@Qualifier(value="userService") 
//@Qualifier("userService")     //value属性可以省略不写
private UserService userService;
```

### 2.3. @Resource

**作用：**

```
@Autowire和@Resource都是Spring支持的注解形式动态装配bean的方式。@Resource默认按照名称(byName)装配，名称可以通过name属性指定。如果没有指定name，则注解在字段上时，默认取（name=字段名称）装配。如果注解在setter方法上时，默认取（name=属性名称）装配。
```

**属性：**

```
name：用于指定要注入的bean的id
type：用于指定要注入的bean的type
```

**装配顺序**

```
1.如果同时指定name和type属性，则找到唯一匹配的bean装配，未找到则抛异常；
2.如果指定name属性，则按照名称(byName)装配，未找到则抛异常；
3.如果指定type属性，则按照类型(byType)装配，未找到或者找到多个则抛异常；
4.既未指定name属性，又未指定type属性，则按照名称(byName)装配；如果未找到，则按照类型(byType)装配。
```

**案例：**

```java
@Resource(name="userService")
//@Resource(type="userService")
//@Resource(name="userService", type="UserService")
private UserService userService;
```

### 2.4. @Value

**作用：**

```
通过@Value可以将外部的值动态注入到Bean中，可以为基本类型数据和String类型数据的变量注入数据
```

**案例：**

```java
// 1.基本类型数据和String类型数据的变量注入数据
@Value("tom") 
private String name;
@Value("18") 
private Integer age;
// 2.从properties配置文件中获取数据并设置到成员变量中
// 2.1 jdbcConfig.properties配置文件定义如下
jdbc.driver \= com.mysql.jdbc.Driver  
jdbc.url \= jdbc:mysql://localhost:3306/eesy  
jdbc.username \= root  
jdbc.password \= root
// 2.2 获取数据如下
@Value("${jdbc.driver}")  
private String driver;
@Value("${jdbc.url}")  
private String url;  
@Value("${jdbc.username}")  
private String username;  
@Value("${jdbc.password}")  
private String password;
//3.静态资源获取
public class Demo{
    private static String baseKey;

    @Value("${base.key}")
    public void setBaseKey(String baseKey){
		Demo.baseKey=baseKey;
    }
    //这样 baseKey静态属性 就赋值了
}
```

## 3. 用于改变bean作用范围的注解

### 3.1. @Scope

**作用：**

```
指定bean的作用范围。
```

**属性：**

```
value：
    1）singleton：单例
    2）prototype：多例
    3）request： 
    4）session： 
    5）globalsession：
```

**案例：**

```java
@Autowire
@Scope(value="prototype")
private UserService userService;
```

## 4. 生命周期相关的注解

### 4.1. @PostConstruct

**作用：**

```
指定初始化方法
```

**案例：**

```java
@PostConstruct  
public void init() {  
    System.out.println("初始化方法执行");  
}
```

### 4.2. @PreDestroy

**作用：**

```
指定销毁方法
```

**案例：**

```java
@PreDestroy  
public void destroy() {  
    System.out.println("销毁方法执行");  
}
```

## 5. 配置类相关的注解

## 6. 启动类注解

### 6.1 @SpringBootApplication

### 6.2 @Import

### 6.3 @ComponentScan

**定义** :

```java

@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Repeatable(ComponentScans.class)
public @interface ComponentScan {
    // 用于指定包的路径，进行扫描
    @AliasFor("basePackages")
    String[] value() default {};
    
    //用于指定某个类的包的路径进行扫描
    @AliasFor("value")
    String[] basePackages() default {};
    Class<?>[] basePackageClasses() default {};
    
    //bean的名称的生成器
    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;
    Class<? extends ScopeMetadataResolver> scopeResolver() default AnnotationScopeMetadataResolver.class;
    ScopedProxyMode scopedProxy() default ScopedProxyMode.DEFAULT;
    String resourcePattern() default "**/*.class";
    
    //是否开启对@Component，@Repository，@Service，@Controller的类进行检测
    boolean useDefaultFilters() default true;
    /**includeFilters: 包含的过滤条件
            FilterType.ANNOTATION：按照注解过滤
            FilterType.ASSIGNABLE_TYPE：按照给定的类型
            FilterType.ASPECTJ：使用ASPECTJ表达式
            FilterType.REGEX：正则
            FilterType.CUSTOM：自定义规则
    */
    ComponentScan.Filter[] includeFilters() default {};
    //排除的过滤条件，用法和includeFilters一样
    ComponentScan.Filter[] excludeFilters() default {};
    boolean lazyInit() default false;
    @Retention(RetentionPolicy.RUNTIME)
    @Target({})
    public @interface Filter {
        FilterType type() default FilterType.ANNOTATION;
        @AliasFor("classes")
        Class<?>[] value() default {};
        @AliasFor("value")
        Class<?>[] classes() default {};
        String[] pattern() default {};
    }
}
```

//TODO 待续
