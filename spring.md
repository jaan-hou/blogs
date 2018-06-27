# Spring
## Spring Bean 装配
- 自动配置
    - 组件扫描

        发现应用上下文中所创建的 bean
        - bean 要有 @Component 注解
        - bean 要有 @ComponentScan 注解启用组件扫描, 或者通过 xml 启用组件扫描
        ```
        <context: component-scan base-package="xxx">
        ``` 
        @Component 注解指明扫描的基础包
        ```
        @Component(basePackages="xxx")
        ```
        指明多个要扫描的基础包
        ```
        @Component(basePackages={"xxx","yyy"})
        ```
        或者指定为包中所包含的类或者接口
        ```
        @Component(basePackageClasses={XX.class,YY.class})
        ```
    - 自动装配

        Spring 自动满足 bean 之间的依赖, 主要是用 @Autowired 注解, 可以用到类的任何方法或属性上(构造器, Setter 方法等)

    - 处理自动装配的歧义性
        - 用 @Primary 注解标识首选的 bean
        - 用 @Qualifier 注解限定自动装配的 bean, @Qualifier 注解的参数是想要注入的 bean 的id, 默认生成的 bean 的 id 是类名以小写字母开头, 这样把限定符和类名耦合在了一起, 改了类名之后限定符会失败, 如有必要更好的方法是给 bean 创建自定义的限定符

- java 中显式配置

    比如你要装配第三方库中的组件, 当然没有办法在它的类上去添加 @Component 注解, 必须使用显式装配的方法
    - 创建 Java Config 类

        添加 @Configuration 注解
    - 在 Java Config类中声明 bean

        写一个方法, 添加 @Bbean 注解, 告诉 Spring 这个方法将返回一个对象注册为 Spring 应用上下文中的 bean ,方法中包含产生 bean 实例的逻辑
    - 在 Java Config 类中注入 bean
        - 借助构造器注入
        ```
        @Bean
        public YourClass yourClass(){
            return new YourClass(调用创建所需类型的实例的方法);
        }
        ```
        - 借助 Setter 方法注入
- xml 中显式配置
    - 创建 xml 配置
    - 在 xml 中声明简单的 bean 
    ```
    <bean id="xxx" class="yyy" />
    ```
    - 构造器中注入 bean 引用
    ```
    <bean id="xxx" class="yyy" >
        <constructor-arg ref="要注入的 bean id" />
    </bean>
    ```
    或者使用 Spring 的 c-命名空间, 
    ```
    <bean id="xxx" class="yyy" >
        c:参数名-ref="要注入的 bean id" 
    </bean>
    ``` 
    更好的做法是用参数的顺序取代参数名
    ```
    <bean id="xxx" class="yyy" >
        c:_0-ref="要注入的 bean id" />
    </bean>
    ```
    只有一个参数的时候不需要指明参数名
    ```
    <bean id="xxx" class="yyy" >
        c:_-ref="要注入的 bean id" />
    </bean>
    ```
    - 将字面量注入构造器
    ```
    <bean id="xxx" class="yyy" >
        <constructor-arg value="hello" />
        <constructor-arg value="world" />
    </bean>
    ```
    使用 c-命名空间
    ```
    <bean id="xxx" class="yyy" >
        c:_参数名1="hello"
        c:_参数名2="world" />
    ```
    同样, 也可以使用 c:_0="xxx"这样指明参数的顺序而不是参数名, 
    只有一个参数的时候可以不指明参数名, 即c:_="hello"
    - 装配集合参数
    ```
    <constructor-arg>
        <list>
            <value>hello</value>
            <value>world</value>
        </list>
    </constructor-arg>
    ```
    装配引用
    ```
    <constructor-arg>
        <list>
            <ref bean="hello" />
            <ref bean="world" />
        </list>
    </constructor-arg>
    ```
    set 和 list 的区别是 set 元素会忽略重复的值
    - 装配属性
    ```
    <bean id="xxx" class="yyy">
        <property name="name" ref="hza" />
    </bean>
    ```
    也可以使用 p-命名空间
    ```
    <bean id="xxx" class="yyy" p:name-ref="hza" />
    ```
    不能使用 p-命名空间装配集合,但可以借助 
    ```
    <util:list id="trackList">
        <value>hello</value>
    </util:list>
    ```
    - 导入和混合配置

    @Import 注解和 @ImportResource 注解

## Spring bean 的作用域

使用 xml 配置 bean 的时候, 可以使用 <bean> 元素的 scope 属性来设置作用域

- 单例(默认)

  在整个应用中, 只创建 bean 的一个实例
- 原型

  每次注入或者通过 Spring 上下文获取的时候, 都会创建一个新的实例, 使用 @Scope("prototype") 或者 @Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
- 会话

  Web 应用中, 为每个会话创建一个实例, 使用 @Scope(value=WebApplicationContext.SCOPE_SESSION,proxyMode=ScopedProxyMode.INTERFACES) 或者 @Scope(value=WebApplicationContext.SCOPE_SESSION,proxyMode=ScopedProxyMode.TARGET_CLASS)
- 请求

  Web 应用中, 为每个请求创建一个实例

