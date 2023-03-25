springboot入门

**1 引入maven依赖**

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
    </parent>


    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

    </dependencies>
```

**2 在源文件下创建主类**

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

//固定写法
@SpringBootApplication
public class BootHelloApplication {

    public static void main(String[] args) {
        SpringApplication.run(BootHelloApplication.class, args);
    }
    
}
```

**3 编写业务(在源文件下新建包controller,在其中创建控制器)**

```java
/*

    相当于
    @ResponseBody   :表示方法返回的是消息数据,不是页面路径.可以在类上注释,也可以在方法上注释.
                    在类上注释,则类中所有方法都返回消息数据
    @Controller   :控制器注释

 */
@RestController
public class TestController {

    @RequestMapping("/")
    public String index(){
        System.out.println("okokok");
        return "Hello boot!";
    }
}
```

**4 测试运行**
	直接运行main方法

**5 简化配置**

application.properties

**6 简化部署**

```xml
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

把项目打成jar包，直接在目标服务器执行即可。 	

springboot特点

**依赖管理**

父项目做依赖管理(版本自动配置,也可以修改默认版本号)

开发导入starter场景启动器

```xml
1、见到很多 spring-boot-starter-* ： *就某种场景
2、只要引入starter，这个场景的所有常规需要的依赖我们都自动引入
3、SpringBoot所有支持的场景
https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter
4、见到的  *-spring-boot-starter： 第三方为我们提供的简化开发的场景启动器。
5、所有场景启动器最底层的依赖
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
  <version>2.3.4.RELEASE</version>
  <scope>compile</scope>
</dependency>
```



# springboot 特点

## 自动配置

自动配置好Tomcat

自动配置springMVC

自动配置好Web常见功能 如:字符编码问题

默认的包结构
	主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来
	无需以前的包扫描配置
	想要改变扫码路径 :@SpringBootApplication(scanBasePackages=**"com.atguigu"**):
									或者@ComponentScan 指定扫描路径

- ```java
  @SpringBootApplication
  等同于
  @SpringBootConfiguration
  @EnableAutoConfiguration
  @ComponentScan("com.atguigu.boot")
  ```

- 

各种配置拥有默认值

- 默认配置最终都是映射到某个类上
- 配置文件的值最终会绑定每个类上，这个类会在容器中创建对象

按需加载所有自动配置项

- - 非常多的starter
  - 引入了哪些场景这个场景的自动配置才会开启
  - SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面

# 容器功能

## 组件添加

**@Configuration	@Bean**
给容器添加组件

```java
//@Configuration //告诉springboot这是一个配置类 (等于配置文件)
@Configuration(proxyBeanMethods = true) //proxyBeanMethods = true :单实例对象(默认为true)
public class MyConfig {

    @Bean   //给容器添加组件. 组件id:方法名
    public SB sb01(){

        return new SB("Jaylen",19,100);
    }

    @Bean("bigSB")   //指定组件id
    public SB sb02(){

        return new SB("Mimi",20,99);
    }

}
```

在主方法中调用容器中的组件

```java
@SpringBootApplication
public class BootHelloApplication {

    public static void main(String[] args) {
        //返回IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(BootHelloApplication.class, args);

        //查看容器里的组件
//        String[] names = run.getBeanDefinitionNames();
//        for(String name : names){
//            System.out.println(name);
//        }

        SB sb01 = run.getBean("sb01",SB.class);
        System.out.println(sb01.toString());

        SB sb02 = run.getBean("sb01",SB.class);
        //@Bean注解给容器添加组件默认是单实例的(对同一个组件id获取对象,获取到的实际上是同一个)
        System.out.println(sb01.equals(sb02));

        SB sb03 = run.getBean("bigSB",SB.class);
        System.out.println(sb03.toString());

        //configuration也是一个组件
        MyConfig myConfig = run.getBean(MyConfig.class);
        System.out.println(myConfig);
    }
    
}
```

注意:proxyBeanMethods属性:

>最佳实践:组件依赖
>
>true:	Full模式(配置类组件之间有依赖关系，方法会被调用得到之前单实例组件，用Full模式)
>False:	Lite模式(配置 类组件之间无依赖关系用Lite模式加速容器启动过程，减少判断)



**@Import**

**条件注入**

>当满足某些条件时,注解下的组件才被注入



**@ConfigurationProperties(...)**

>为bean在配置文件中寻找指定的属性值并赋值



# 开发小技巧

## Lomcat

简化JavaBean开发

```java
@Data
@ToString
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class SB {
    private String name;
    private int age;
    private int sbLevel;
}
```

简化日志开发	@Slf4j

```java
@Slf4j
@SpringBootApplication
public class BootHelloApplication {

    public static void main(String[] args) {
        //返回IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(BootHelloApplication.class, args);
        log.info("成功打印日志");
    }
    
}
```

## Spring initializr

创建springboot项目

# yaml

## 基本语法

- key: value；kv之间有空格
- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释
- 字符串无需加引号，如果要加，''与""表示字符串内容 会被 转义/不转义

# Web开发

## 静态资源访问

### 静态资源目录

只要静态资源放在类路径下： called `/static` (or `/public` or `/resources` or `/META-INF/resources`

访问 ： 当前项目根路径/ + 静态资源名 



原理： 静态映射/**。

请求进来，先去找Controller看能不能处理。不能处理的所有请求又都交给静态资源处理器。静态资源也找不到则响应404页面

**改变默认的静态资源路径**

```yaml
spring:
  mvc:
    static-path-pattern: /res/** # 改变静态资源默认访问路径

  resources:
    static-locations: [classpath:/haha/] # 改变静态资源存放文件夹路径
```

### 欢迎页

将index.html放在静态资源文件夹下,直接访问根路径就是index.html,无需配置

### 小图标

favicon.ico 放在静态资源文件夹下

## 请求处理

### 从URL中获取参数

```java
//@PathVariable处理请求参数
    @GetMapping("/test01/{name}/{age}/{sbLevel}")   //注意:这里必须在注释中写明参数名
    public Map<String,Object> test01(@PathVariable String name,
                                     @PathVariable int age,
                                     @PathVariable int sbLevel,
                                     @PathVariable Map<String,Object> pv
                                     ){

        Map<String,Object> map = new HashMap<>();
        map.put("name",name);
        map.put("age",age);
        map.put("sbLevel",sbLevel);
        map.put("pv",pv);

        return map;
    }

    //@RequestParam处理请求参数
    @GetMapping("/test02")  //这里无需像@pathVariable一样写明参数名
    public Map<String,Object> test02(@RequestParam String name,
                                     @RequestParam int age,
                                     @RequestParam int sbLevel,
                                     @RequestParam Map<String,Object> params){

        Map<String,Object> map = new HashMap<>();
        map.put("name",name);
        map.put("age",age);
        map.put("sbLevel",sbLevel);
        map.put("params",params);

        return map;
    }
```

### 获取请求头和cookie

```java
//@RequestHeader获取请求头信息
    @GetMapping("/test03")  //这里无需像@pathVariable一样写明参数名
    public Map<String,Object> test03(@RequestHeader("User-Agent") String userAgent,//一串userAgent
                                     @RequestHeader Map<String,Object> header){//将userAgent中的信息一一对应到map中

        Map<String,Object> map = new HashMap<>();
        map.put("userAgent",userAgent);
//        map.put("header",header);

        return map;
    }

    //@CookieValue获取cookie信息
    @GetMapping("/test04")
    public Map<String, Object> getCookie(@CookieValue("Idea-c72d2eb9") String cookie001,//获取cookie信息
                                         @CookieValue("Idea-c72d2eb9") Cookie cookie //获取cookie对象
    ){

        Map<String,Object> map = new HashMap<>();

        map.put("cookie",cookie001);
        System.out.println(cookie.getName() + " " +cookie.getValue());

        return map;
    }
```

### 处理form表单提交的数据

```java
    //@RequestBody处理表单提交的请求数据
    @PostMapping("/test05")
    //这里注意html中form表单的各<input>中要写name属性,否则会报错
    //若未加name属性,需要在注解中写入以下参数
    // @RequestBody(required = false)
    public Map postMethod(@RequestBody String content){
        Map<String,Object> map = new HashMap<>();
        map.put("content",content);
        return map;
    }
```

```java
	@ResponseBody
    @PostMapping("/insert")
    public void insert(SB sb){ //将表单提交的数据封装到对象中
        sbService.insert(sb);
    }
```



### @Controller和@RestController的区别

@RestController 被@Controller 和@ResponseBody标注，即拥有二者的注解语义，因此在注解处理时@RestController 比@Controller多了@ResponseBody 语义，这就是@RestController 和@Controller 的区别，也是@RestController 的返回值为何都是经过转换的 json 都原因。



@RestController默认所有方法返回的是数据

@Controller默认所有方法返回的是页面路径,若要返回数据,需在方法前注释@RespenceBody

### 获取request作用域中的数据

```java
@Controller
public class Test02Controller {

    @GetMapping("/test0201")
    public String test0201(HttpServletRequest req){
        req.setAttribute("Name","Jaylen!");
        req.setAttribute("Age",100);

        return "/test0202";
    }

    @ResponseBody
    @GetMapping("/test0202")
    public Map<String,Object> test0202(@RequestAttribute("Name") String name,
                                        @RequestAttribute("Age") int age){

        Map<String,Object> map = new HashMap<>();

        map.put("Age",age);
        map.put("Name",name);
        return map;
    }
}
```

## thymeleaf

**导入依赖**

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```



**html文件中声明thymeleaf**

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

**入门**
在要渲染的标签里加 th:...
域中的数据用${}包起来

用thymeleaf渲染的html文件放在templates文件夹下
templates文件夹下的html文件访问时,路径中不用加html后缀

示例:

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1 th:text="${msg}">哈哈</h1>
<h2>
    <a href="www.atguigu.com" th:href="${link}">去百度</a>  <br/>
    <a href="www.atguigu.com" th:href="@{/link}">去百度2</a>
</h2>
</body>
</html>
```

## 拦截器

### 创建拦截器

实现HandlerInterceptor接口

```java
//登录检查
public class LoginInterceptor implements HandlerInterceptor {
    //目标方法执行前
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        if(session.getAttribute("loginUser") != null){
            //放行
            return true;
        }

        //拦截住,返回登录页,要求登录
        request.setAttribute("msg","Please login first");
        request.getRequestDispatcher("/").forward(request,response);
        return false;
    }

    //目标方法执行完后
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }

    //页面渲染以后
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}
```

### 将拦截器注册到容器中

实现WebMvcConfigurer接口的addInterceptors方法

```java
@Configuration
public class AdminWebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor()) //注册拦截器
                .addPathPatterns("/**") //要拦截哪些路径   /**代表全部路径(包括静态资源)
                .excludePathPatterns("/","/login","/css/**","/js/**"); //哪些路径除外
                //若static文件夹下子文件夹太多,可以设置静态资源访问路径spring.mvc.static-path-pattern=/static/**
                //然后再同意设置该路径下的资源可以访问
    }
}
```

## 文件上传

### HTML固定写法

```html
<!--    固定写法-->
    <form role="form" th:action="@{/upload}" method="post" enctype="multipart/form-data">
        单文件上传<input type="file" name="file"><br/>
        多文件上传<input type="file" name="files" multiple><br/>
        <input type="submit">
    </form>
```

### 控制器中处理文件上传

```java
@Slf4j
@Controller
public class UploadController {

    @PostMapping("/upload")
    public String upload(@RequestPart("file") MultipartFile file,
                         @RequestPart("files") MultipartFile[] files) throws IOException {

        log.info("获取到上传的文件: file1{} , file2{}",file ,files);

        //将上传的文件存储到本地
        //单文件
        if(!file.isEmpty()){
            String originalFileName = file.getOriginalFilename();
            file.transferTo(new File("D:\\desktop\\upload\\"+originalFileName));
        }

        //多文件
        if(files.length > 0){
            for(MultipartFile f : files){
                if(!f.isEmpty()){
                    String originalFileName = f.getOriginalFilename();
                    f.transferTo(new File("D:\\desktop\\upload\\"+originalFileName));
                }
            }
        }

        return "updateSucceeded";
    }

    @GetMapping("/upload")
    public String goToUpload(){

        return "upload";
    }
}
```

### 设置上传文件大小限制

```properties
#单个文件大小
spring.servlet.multipart.max-file-size=10MB
#请求总共可上传文件大小
spring.servlet.multipart.max-request-size=100MB
```

## 自定义错误页面

### 新建错误页面

templates文件夹下新建erorr文件夹
该文件夹下新建4xx.html文件:当有4开头的错误时,都会显示这个页面(也可以单独处理一个错误:404.html)

### 显示错误信息

可以打印错误信息	如:

下面的错误信息中的error,message是固定名称,还要许多其它的

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>傻逼,这都能搞错?</h1>
    <p th:text="${error}"></p>
    <p th:text="${message}"></p>
</body>
</html>
```

## 原生组件注入

### servlet

@WebServlet注解	继承HttpServlet

```java
@WebServlet(urlPatterns = "/my")
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("66666");
    }
}
```

设置扫描路径(主类中)

```java
@ServletComponentScan(basePackages = "com.admin")
@SpringBootApplication
public class AdminApplication {

    public static void main(String[] args) {
        SpringApplication.run(AdminApplication.class, args);
    }

}
```

### Filter

```java
@Slf4j
@WebFilter(urlPatterns = {"/update"})    //  /*是servlet中的写法;/**是springboot中的写法   都表示该文件夹下的所用文件
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        log.info("MyFilter初始化完成");
    }

    @Override
    public void destroy() {
        log.info("MyFilter销毁");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        log.info("MyFilter工作");
    }
}
```

### LIstener

```java
@Slf4j
@WebListener
public class MyServletContextListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        log.info("监听到项目初始化完成");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        log.info("监听到项目销毁");
    }
}
```

# RegistConfig

```java
//proxyBeanMethods = true   保证依赖的组件始终是单实例的
@Configuration(proxyBeanMethods = true)
public class MyRegistConfig {

    @Bean
    public ServletRegistrationBean myServlet(){
        MyServlet myServlet = new MyServlet();

        return new ServletRegistrationBean(myServlet,"/my");
    }

    @Bean
    public FilterRegistrationBean myFilter(){
        MyFilter myFilter = new MyFilter();

//        return new FilterRegistrationBean(myFilter,myServlet());//myServlet拦截啥路径它就拦截啥路径
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
        filterRegistrationBean.setUrlPatterns(Arrays.asList("/my"));

        return filterRegistrationBean;
    }

    @Bean
    public ServletListenerRegistrationBean myListener(){
        MyServletContextListener myServletContextListener = new MyServletContextListener();

        return new ServletListenerRegistrationBean(myServletContextListener);
    }
}
```

## 定制化原理



# SQL数据访问

## 数据源的自动配置- HikariDataSource

### 导入JDBC场景
(导入了数据源,jdbc,事务,但是没有导入数据库驱动)

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jdbc</artifactId>
        </dependency>
```

为什么导入JDBC场景不导入数据库驱动?	官方不知道你要用哪个驱动呀.

### 导入数据库驱动

mysql

```xml
默认版本：<mysql.version>8.0.22</mysql.version>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
<!--            <version>5.1.49</version>-->
        </dependency>
想要修改版本
1、直接依赖引入具体版本（maven的就近依赖原则）
2、重新声明版本（maven的属性的就近优先原则）
    <properties>
        <java.version>1.8</java.version>
        <mysql.version>5.1.49</mysql.version>
    </properties>
```

### 分析自动配置

- DataSourceAutoConfiguration ： 数据源的自动配置

- - 修改数据源相关的配置：**spring.datasource**
  - **数据库连接池的配置，是自己容器中没有DataSource才自动配置的**
  - 底层配置好的连接池是：**HikariDataSource**

```java
@Configuration(proxyBeanMethods = false)
	@Conditional(PooledDataSourceCondition.class)
	@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
	@Import({ DataSourceConfiguration.Hikari.class, DataSourceConfiguration.Tomcat.class,
			DataSourceConfiguration.Dbcp2.class, DataSourceConfiguration.OracleUcp.class,
			DataSourceConfiguration.Generic.class, DataSourceJmxConfiguration.class })
	protected static class PooledDataSourceConfiguration
```

- DataSourceTransactionManagerAutoConfiguration： 事务管理器的自动配置
- JdbcTemplateAutoConfiguration： **JdbcTemplate的自动配置，可以来对数据库进行crud**

- - 可以修改这个配置项@ConfigurationProperties(prefix = **"spring.jdbc"**) 来修改JdbcTemplate
  - @Bean@Primary    JdbcTemplate；容器中有这个组件

- JndiDataSourceAutoConfiguration： jndi的自动配置
- XADataSourceAutoConfiguration： 分布式事务相关的

### 修改配置项

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mybase01?serverTimezone=UTC
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver
```

**注意:** URL最后加上?serverTimezone=UTC

### 测试

```java
@Slf4j
@SpringBootTest
class SbApplicationTests {

    @Autowired //自动注入
    JdbcTemplate jdbcTemplate;

    @Test
    void contextLoads() {
        Long along = jdbcTemplate.queryForObject("select count(*) from sbrank",Long.class);
        log.info("记录总数:" + along);
    }

}
```

## 使用Druid数据源

### druid官方github地址

<https://github.com/alibaba/druid>

### 自定义方式

#### 创建数据源

```xml
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.17</version>
        </dependency>
```

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
		destroy-method="close">
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
		<property name="maxActive" value="20" />
		<property name="initialSize" value="1" />
		<property name="maxWait" value="60000" />
		<property name="minIdle" value="1" />
		<property name="timeBetweenEvictionRunsMillis" value="60000" />
		<property name="minEvictableIdleTimeMillis" value="300000" />
		<property name="testWhileIdle" value="true" />
		<property name="testOnBorrow" value="false" />
		<property name="testOnReturn" value="false" />
		<property name="poolPreparedStatements" value="true" />
		<property name="maxOpenPreparedStatements" value="20" />
```

```java
// 默认的自动配置是判断容器中没有才会配@ConditionalOnMissingBean(DataSource.class)
    @ConfigurationProperties("spring.datasource") //在方法中用set配置的属性,都可以再配置文件中配置
    @Bean
    public DataSource dataSource() throws SQLException {
        DruidDataSource druidDataSource = new DruidDataSource();

//        druidDataSource.setUrl();
//        druidDataSource.setUsername();
//        druidDataSource.setPassword();

        //加入监控功能和防火墙
//        druidDataSource.setFilters("stat,wall");

        return druidDataSource;
    }
```

#### StatViewServlet

StatViewServlet的用途包括：

- 提供监控信息展示的html页面
- 提供监控信息的JSON API

```java
/**
     * 配置 druid的监控页功能(能访问druid的监控页)
     * @return
     */
    @Bean
    public ServletRegistrationBean statViewServlet(){
        StatViewServlet statViewServlet = new StatViewServlet();
        ServletRegistrationBean<StatViewServlet> registrationBean = new ServletRegistrationBean<>(statViewServlet, "/druid/*");

        //设置账号密码
        registrationBean.addInitParameter("loginUsername","jaylen");
        registrationBean.addInitParameter("loginPassword","iambigsb");

        return registrationBean;
    }
```

#### StatFilter

用于统计监控信息；如SQL监控、URI监控

```java
/**
     * WebStatFilter 用于采集web-jdbc关联监控的数据。
     */
    @Bean
    public FilterRegistrationBean webStatFilter(){
        WebStatFilter webStatFilter = new WebStatFilter();

        FilterRegistrationBean<WebStatFilter> filterRegistrationBean = new FilterRegistrationBean<>(webStatFilter);
        filterRegistrationBean.setUrlPatterns(Arrays.asList("/*"));
        filterRegistrationBean.addInitParameter("exclusions","*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");

        return filterRegistrationBean;
    }
```

### 使用官方starter方式

即在配置文件中配置即可,无需向自定义方式中在config中配置

#### 引入druid-starter

```xml
 <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.17</version>
        </dependency>
```

#### 分析自动配置

- 扩展配置项 **spring.datasource.druid**
- DruidSpringAopConfiguration.**class**,   监控SpringBean的；配置项：**spring.datasource.druid.aop-patterns**
- DruidStatViewServletConfiguration.**class**, 监控页的配置：**spring.datasource.druid.stat-view-servlet；默认开启**
-  DruidWebStatFilterConfiguration.**class**, web监控配置；**spring.datasource.druid.web-stat-filter；默认开启**
- DruidFilterConfiguration.**class**}) 所有Druid自己filter的配置

```java
    private static final String FILTER_STAT_PREFIX = "spring.datasource.druid.filter.stat";
    private static final String FILTER_CONFIG_PREFIX = "spring.datasource.druid.filter.config";
    private static final String FILTER_ENCODING_PREFIX = "spring.datasource.druid.filter.encoding";
    private static final String FILTER_SLF4J_PREFIX = "spring.datasource.druid.filter.slf4j";
    private static final String FILTER_LOG4J_PREFIX = "spring.datasource.druid.filter.log4j";
    private static final String FILTER_LOG4J2_PREFIX = "spring.datasource.druid.filter.log4j2";
    private static final String FILTER_COMMONS_LOG_PREFIX = "spring.datasource.druid.filter.commons-log";
    private static final String FILTER_WALL_PREFIX = "spring.datasource.druid.filter.wall";
```

#### 配置示例

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/db_account
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver

    druid:
      aop-patterns: com.atguigu.admin.*  #监控SpringBean
      filters: stat,wall     # 底层开启功能，stat（sql监控），wall（防火墙）

      stat-view-servlet:   # 配置监控页功能
        enabled: true
        login-username: admin
        login-password: admin
        resetEnable: false

      web-stat-filter:  # 监控web
        enabled: true
        urlPattern: /*
        exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'


      filter:
        stat:    # 对上面filters里面的stat的详细配置
          slow-sql-millis: 1000
          logSlowSql: true
          enabled: true
        wall:
          enabled: true
          config:
            drop-table-allow: false

```

SpringBoot配置示例

<https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter>



配置项列表[https://github.com/alibaba/druid/wiki/DruidDataSource%E9%85%8D%E7%BD%AE%E5%B1%9E%E6%80%A7%E5%88%97%E8%A1%A8](https://github.com/alibaba/druid/wiki/DruidDataSource配置属性列表)

## 整合MyBatis

<https://github.com/mybatis>

starter

SpringBoot官方的Starter：spring-boot-starter-*

第三方的： *-spring-boot-starter

```xml
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.4</version>
        </dependency>
```

### 配置模式

- 全局配置文件

- SqlSessionFactory: 自动配置好了

- SqlSession：自动配置了 **SqlSessionTemplate 组合了SqlSession**

- @Import(**AutoConfiguredMapperScannerRegistrar**.**class**）；

- Mapper： 只要我们写的操作MyBatis的接口标准了 **@Mapper 就会被自动扫描进来**

  

```java
@EnableConfigurationProperties(MybatisProperties.class) ： MyBatis配置项绑定类。
@AutoConfigureAfter({ DataSourceAutoConfiguration.class, MybatisLanguageDriverAutoConfiguration.class })
public class MybatisAutoConfiguration{}

@ConfigurationProperties(prefix = "mybatis")
public class MybatisProperties
```

**配置模式流程:**

导入mybatis依赖:

编写mapper接口。标准@Mapper注解

```java
@Mapper
public interface AccountMapper {


//    <select id="getAcct" resultType="com.sb.bean.Account">
    public Account getAcct(Long id);
}
```



编写sql映射文件并绑定mapper接口

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.sb.mapper.AccountMapper">

    <!--    public Account getAcct(Long id); -->
    <select id="getAcct" resultType="com.sb.bean.Account">
        select * from account where id=#{id}
    </select>

    <!--    -->

</mapper>
```



在application.yaml中指定Mapper配置文件的位置，以及指定全局配置文件的信息 （建议；**配置在mybatis.configuration**）

```yaml
# 配置mybatis规则
mybatis:
#  config-location: classpath:mybatis/mybatis-config.xml
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration:
    map-underscore-to-camel-case: true
#配置 **private** Configuration **configuration**; mybatis.**configuration下面的所有，就是相当于改mybatis全局配置文件中的值**    
# 可以不写全局；配置文件，所有全局配置文件的配置都放在configuration配置项中即可
```



service调用mapper,controller调用service

bean

```java
import lombok.Data;

@Data
public class Account {

    private Long id;
    private String userId;
    private Integer money;

}
```

service

```java
@Service
public class AccountServiceImpl AccountService{

    @Autowired(required = false)
    AccountMapper accountMapper;

    public Account getAcctById(Long id){
        return accountMapper.getAcct(id);
    }
}
```

controller

```java
@Autowired
    AccountService accountService;

// ....

@ResponseBody
    @GetMapping("/acct")
    public Account getAcct(@RequestParam("id") Long id){

        return accountService.getAcctById(id);
    }
```

### 注解模式

> 不需要配置mapper的xml文件了

```java
@Mapper
public interface CityMapper {

    @Select("select * from city where id=#{id}")
    public City getCity(Long id);
}
```

```java
@Mapper
public interface SBMapper {
    @Insert("insert into sbRank values(#{name},#{age},#{sbLevel})")
    public void insert(SB sb);
}
```

```java
	@Insert("insert into  city(`name`,`state`,`country`) values(#{name},#{state},#{country})")
    @Options(useGeneratedKeys = true,keyProperty = "id") //设置主键自增
    public void insert(City city);
```



### 注意

> 注解和配置可以混合使用

```xml
<!--    public void insert(City city);-->
    <insert id="insert" useGeneratedKeys="true" keyProperty="id"><!--设置主键自增-->
        insert into  city(`name`,`state`,`country`) values(#{name},#{state},#{country})
    </insert>
```

## 整合Mybatis-Plus

### 简介

什么是MyBatis-Plus?

[MyBatis-Plus](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis](http://www.mybatis.org/mybatis-3/) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

[mybatis plus 官网](https://baomidou.com/)

建议安装 **MybatisX** 插件 

### 整合

引入依赖

```xml
<!-- 引入了mp之后jdbc和mb都不用引了 -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version></version>
        </dependency>
```

> 只需要我们的Mapper继承 **BaseMapper** 就可以拥有crud能力



自动配置原理

>自动配置
>
>- MybatisPlusAutoConfiguration 配置类，MybatisPlusProperties 配置项绑定。**mybatis-plus：xxx 就是对****mybatis-plus的定制**
>- **SqlSessionFactory 自动配置好。底层是容器中默认的数据源**
>- **mapperLocations 自动配置好的。有默认值。****classpath\*:/mapper/\**/\*.xml；任意包的类路径下的所有mapper文件夹下任意路径下的所有xml都是sql映射文件。  建议以后sql映射文件，放在 mapper下**
>- **容器中也自动配置好了** **SqlSessionTemplate**
>- **@Mapper 标注的接口也会被自动扫描；建议直接** @MapperScan(**"com.atguigu.admin.mapper"**) 批量扫描就行

### CRUD

Mapper

```java
@Mapper
public interface UserMapper extends BaseMapper<User> {
}
```

Bean

> 表中没有的字段可以用@TableField(exist = false)注释

```java
@Data
public class User {
    private String name;
    @TableField(exist = false)//标明数据库表中不存在该字段,使用mp时不会对表操作该字段
    private String password;

    private Long id;
    private Integer age;
    private String email;
}
```

Service

```java
@Service
public class UserServiceImpl implements UserService {
    @Autowired(required = false)
    UserMapper userMapper;

    public List<User> selectList(){
        return userMapper.selectList(null);
    }
}
```

Controller

```java
@Controller
public class UserController {
    @Autowired
    UserServiceImpl userService;

    @ResponseBody
    @GetMapping("/user")
    public List<User> selectAllUser(){
        return userService.selectList();
    }
}
```

# 单元测试

## JUnit5的变化

>**Spring Boot 2.2.0 版本开始引入 JUnit 5 作为单元测试默认库**
>
>作为最新版本的JUnit框架，JUnit5与之前版本的Junit框架有很大的不同。由三个不同子项目的几个不同模块组成。
>
>**JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage**
>
>**JUnit Platform**: Junit Platform是在JVM上启动测试框架的基础，不仅支持Junit自制的测试引擎，其他测试引擎也都可以接入。
>
>**JUnit Jupiter**: JUnit Jupiter提供了JUnit5的新的编程模型，是JUnit5新特性的核心。内部 包含了一个**测试引擎**，用于在Junit Platform上运行。
>
>**JUnit Vintage**: 由于JUint已经发展多年，为了照顾老的项目，JUnit Vintage提供了兼容JUnit4.x,Junit3.x的测试引擎。
>
>
>
>注意：
>
>**SpringBoot 2.4 以上版本移除了默认对** **Vintage 的依赖。如果需要兼容junit4需要自行引入（不能使用junit4的功能 @Test****）**
>
>**JUnit 5’s Vintage Engine Removed from** `**spring-boot-starter-test,如果需要继续兼容junit4需要自行引入vintage**`
>
>> ```xml
>> <dependency>
>>     <groupId>org.junit.vintage</groupId>
>>     <artifactId>junit-vintage-engine</artifactId>
>>     <scope>test</scope>
>>     <exclusions>
>>         <exclusion>
>>             <groupId>org.hamcrest</groupId>
>>             <artifactId>hamcrest-core</artifactId>
>>         </exclusion>
>>     </exclusions>
>> </dependency>
>> ```

## 基本使用

引入依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
```

使用

```java
@SpringBootTest
class Boot05WebAdminApplicationTests {


    @Test
    void contextLoads() {

    }
}

```

SpringBoot整合Junit以后。

- 编写测试方法：@Test标注（注意需要使用junit5版本的注解）
- Junit类具有Spring的功能，@Autowired、比如 @Transactional 标注测试方法，测试完成后自动回滚

## JUnit常用注解

>JUnit5的注解与JUnit4的注解有所变化
>
><https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations>
>
>- **@Test :**表示方法是测试方法。但是与JUnit4的@Test不同，他的职责非常单一不能声明任何属性，拓展的测试将会由Jupiter提供额外测试
>- **@ParameterizedTest :**表示方法是参数化测试，下方会有详细介绍
>- **@RepeatedTest :**表示方法可重复执行，下方会有详细介绍
>- **@DisplayName :**为测试类或者测试方法设置展示名称
>- **@BeforeEach :**表示在每个单元测试之前执行
>- **@AfterEach :**表示在每个单元测试之后执行
>- **@BeforeAll :**表示在所有单元测试之前执行
>- **@AfterAll :**表示在所有单元测试之后执行
>- **@Tag :**表示单元测试类别，类似于JUnit4中的@Categories
>- **@Disabled :**表示测试类或测试方法不执行，类似于JUnit4中的@Ignore
>- **@Timeout :**表示测试方法运行如果超过了指定时间将会返回错误
>- **@ExtendWith :**为测试类或测试方法提供扩展类引用

## 断言（assertions）

断言（assertions）是测试方法中的核心部分，用来对测试需要满足的条件进行验证。**这些断言方法都是 org.junit.jupiter.api.Assertions 的静态方法**。JUnit 5 内置的断言可以分成如下几个类别：

**检查业务逻辑返回的数据是否合理。**

**所有的测试运行结束以后，会有一个详细的测试报告；**

### 1、简单断言

用来对单个值进行简单的验证。如：

| 方法            | 说明                                 |
| --------------- | ------------------------------------ |
| assertEquals    | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals | 判断两个对象或两个原始类型是否不相等 |
| assertSame      | 判断两个对象引用是否指向同一个对象   |
| assertNotSame   | 判断两个对象引用是否指向不同的对象   |
| assertTrue      | 判断给定的布尔值是否为 true          |
| assertFalse     | 判断给定的布尔值是否为 false         |
| assertNull      | 判断给定的对象引用是否为 null        |
| assertNotNull   | 判断给定的对象引用是否不为 null      |

```java
@Test
@DisplayName("simple assertion")
public void simple() {
     assertEquals(3, 1 + 2, "simple math");
     assertNotEquals(3, 1 + 1);

     assertNotSame(new Object(), new Object());
     Object obj = new Object();
     assertSame(obj, obj);

     assertFalse(1 > 2);
     assertTrue(1 < 2);

     assertNull(null);
     assertNotNull(new Object());
}
```



### 2、数组断言

通过 assertArrayEquals 方法来判断两个对象或原始类型的数组是否相等

```java
@Test
@DisplayName("array assertion")
public void array() {
 assertArrayEquals(new int[]{1, 2}, new int[] {1, 2});
}
```



### 3、组合断言

assertAll 方法接受多个 org.junit.jupiter.api.Executable 函数式接口的实例作为要验证的断言，可以通过 lambda 表达式很容易的提供这些断言



```java
@Test
@DisplayName("assert all")
public void all() {
 assertAll("Math",
    () -> assertEquals(2, 1 + 1),
    () -> assertTrue(1 > 0)
 );
}
```



### 4、异常断言

在JUnit4时期，想要测试方法的异常情况时，需要用**@Rule**注解的ExpectedException变量还是比较麻烦的。而JUnit5提供了一种新的断言方式**Assertions.assertThrows()** ,配合函数式编程就可以进行使用。

```java
@Test
@DisplayName("异常测试")
public void exceptionTest() {
    ArithmeticException exception = Assertions.assertThrows(
           //扔出断言异常
            ArithmeticException.class, () -> System.out.println(1 % 0));

}
```



### 5、超时断言

Junit5还提供了**Assertions.assertTimeout()** 为测试方法设置了超时时间

```java
@Test
@DisplayName("超时测试")
public void timeoutTest() {
    //如果测试方法时间超过1s将会异常
    Assertions.assertTimeout(Duration.ofMillis(1000), () -> Thread.sleep(500));
}
```



### 6、快速失败

通过 fail 方法直接使得测试失败

```java
@Test
@DisplayName("fail")
public void shouldFail() {
 fail("This should fail");
}
```

## 前置条件（assumptions）

JUnit 5 中的前置条件（**assumptions【假设】**）类似于断言，不同之处在于**不满足的断言会使得测试方法失败**，而不满足的**前置条件只会使得测试方法的执行终止**。前置条件可以看成是测试方法执行的前提，当该前提不满足时，就没有继续执行的必要。

```java
@DisplayName("前置条件")
public class AssumptionsTest {
 private final String environment = "DEV";
 
 @Test
 @DisplayName("simple")
 public void simpleAssume() {
    assumeTrue(Objects.equals(this.environment, "DEV"));
    assumeFalse(() -> Objects.equals(this.environment, "PROD"));
 }
 
 @Test
 @DisplayName("assume then do")
 public void assumeThenDo() {
    assumingThat(
       Objects.equals(this.environment, "DEV"),
       () -> System.out.println("In DEV")
    );
 }
}
```

assumeTrue 和 assumFalse 确保给定的条件为 true 或 false，不满足条件会使得测试执行终止。assumingThat 的参数是表示条件的布尔值和对应的 Executable 接口的实现对象。只有条件满足时，Executable 对象才会被执行；当条件不满足时，测试执行并不会终止。



## 嵌套测试

JUnit 5 可以通过 Java 中的内部类和@Nested 注解实现嵌套测试，从而可以更好的把相关的测试方法组织在一起。在内部类中可以使用@BeforeEach 和@AfterEach 注解，而且嵌套的层次没有限制。



```java
@DisplayName("A stack")
class TestingAStackDemo {

    Stack<Object> stack;

    @Test
    @DisplayName("is instantiated with new Stack()")
    void isInstantiatedWithNew() {
        new Stack<>();
    }

    @Nested
    @DisplayName("when new")
    class WhenNew {

        @BeforeEach
        void createNewStack() {
            stack = new Stack<>();
        }

        @Test
        @DisplayName("is empty")
        void isEmpty() {
            assertTrue(stack.isEmpty());
        }

        @Test
        @DisplayName("throws EmptyStackException when popped")
        void throwsExceptionWhenPopped() {
            assertThrows(EmptyStackException.class, stack::pop);
        }

        @Test
        @DisplayName("throws EmptyStackException when peeked")
        void throwsExceptionWhenPeeked() {
            assertThrows(EmptyStackException.class, stack::peek);
        }

        @Nested
        @DisplayName("after pushing an element")
        class AfterPushing {

            String anElement = "an element";

            @BeforeEach
            void pushAnElement() {
                stack.push(anElement);
            }

            @Test
            @DisplayName("it is no longer empty")
            void isNotEmpty() {
                assertFalse(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when popped and is empty")
            void returnElementWhenPopped() {
                assertEquals(anElement, stack.pop());
                assertTrue(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when peeked but remains not empty")
            void returnElementWhenPeeked() {
                assertEquals(anElement, stack.peek());
                assertFalse(stack.isEmpty());
            }
        }
    }
}
```



## 参数化测试

参数化测试是JUnit5很重要的一个新特性，它使得用不同的参数多次运行测试成为了可能，也为我们的单元测试带来许多便利。



利用**@ValueSource**等注解，指定入参，我们将可以使用不同的参数进行多次单元测试，而不需要每新增一个参数就新增一个单元测试，省去了很多冗余代码。



**@ValueSource**: 为参数化测试指定入参来源，支持八大基础类以及String类型,Class类型

**@NullSource**: 表示为参数化测试提供一个null的入参

**@EnumSource**: 表示为参数化测试提供一个枚举入参

**@CsvFileSource**：表示读取指定CSV文件内容作为参数化测试入参

**@MethodSource**：表示读取指定方法的返回值作为参数化测试入参(注意方法返回需要是一个流)



当然如果参数化测试仅仅只能做到指定普通的入参还达不到让我觉得惊艳的地步。让我真正感到他的强大之处的地方在于他可以支持外部的各类入参。如:CSV,YML,JSON 文件甚至方法的返回值也可以作为入参。只需要去实现**ArgumentsProvider**接口，任何外部文件都可以作为它的入参。

```java
@ParameterizedTest
@ValueSource(strings = {"one", "two", "three"})
@DisplayName("参数化测试1")
public void parameterizedTest1(String string) {
    System.out.println(string);
    Assertions.assertTrue(StringUtils.isNotBlank(string));
}


@ParameterizedTest
@MethodSource("method")    //指定方法名
@DisplayName("方法来源参数")
public void testWithExplicitLocalMethodSource(String name) {
    System.out.println(name);
    Assertions.assertNotNull(name);
}

static Stream<String> method() {
    return Stream.of("apple", "banana");
}
```



## 迁移指南

在进行迁移的时候需要注意如下的变化：

- 注解在 org.junit.jupiter.api 包中，断言在 org.junit.jupiter.api.Assertions 类中，前置条件在 org.junit.jupiter.api.Assumptions 类中。
- 把@Before 和@After 替换成@BeforeEach 和@AfterEach。
- 把@BeforeClass 和@AfterClass 替换成@BeforeAll 和@AfterAll。
- 把@Ignore 替换成@Disabled。
- 把@Category 替换成@Tag。
- 把@RunWith、@Rule 和@ClassRule 替换成@ExtendWith。

# 指标监控

## SpringBoot Actuator

### 简介

未来每一个微服务在云上部署以后，我们都需要对其进行监控、追踪、审计、控制等。SpringBoot就抽取了Actuator场景，使得我们每个微服务快速引用即可获得生产级别的应用监控、审计等功能。



![img](https://cdn.nlark.com/yuque/0/2020/png/1354552/1606886483335-697ee1c1-2f69-43ab-bddc-3a038382319c.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_19%2Ctext_YXRndWlndS5jb20g5bCa56GF6LC3%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)

### 1.x与2.x的不同



![img](https://cdn.nlark.com/yuque/0/2020/png/1354552/1606884394162-ac7f2d8e-7abb-44df-9998-fb0f2705f238.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_30%2Ctext_YXRndWlndS5jb20g5bCa56GF6LC3%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)





### 如何使用

- 引入场景

  ```xml
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-actuator</artifactId>
          </dependency>
  ```

- 访问 <http://localhost:8080/actuator/>**

- 暴露所有监控信息为HTTP

```yaml
management:
  endpoints:
    enabled-by-default: true #暴露所有端点信息
    web:
      exposure:
        include: '*'  #以web方式暴露
```

- 测试

<http://localhost:8080/actuator/beans>

<http://localhost:8080/actuator/configprops>

<http://localhost:8080/actuator/metrics>

<http://localhost:8080/actuator/metrics/jvm.gc.pause>

[http://localhost:8080/actuator/](http://localhost:8080/actuator/metrics)endpointName/detailPath
。。。。。。





### 可视化

<https://github.com/codecentric/spring-boot-admin>

引入依赖

```xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
    <!--版本号要与springboot的版本对应-->
    <version></version>
</dependency>
```

主配置类前添加注释

```java
@EnableAdminServer
```

**注意**

> admin要单独开一个项目

**注册需要监控的客户端**

```xml
<!--同样需要注意版本号-->
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version></version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

**在需要监控的项目中配置admin地址**

```yaml
spring: 
	boot:
    	admin:
      	client:
        	url: http://localhost:8888
        	instance:
          	prefer-ip: true
        application:
      	name: sb
```





## Actuator Endpoint

### 最常使用的端点



| ID                 | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `auditevents`      | 暴露当前应用程序的审核事件信息。需要一个`AuditEventRepository组件`。 |
| `beans`            | 显示应用程序中所有Spring Bean的完整列表。                    |
| `caches`           | 暴露可用的缓存。                                             |
| `conditions`       | 显示自动配置的所有条件信息，包括匹配或不匹配的原因。         |
| `configprops`      | 显示所有`@ConfigurationProperties`。                         |
| `env`              | 暴露Spring的属性`ConfigurableEnvironment`                    |
| `flyway`           | 显示已应用的所有Flyway数据库迁移。 需要一个或多个`Flyway`组件。 |
| `health`           | 显示应用程序运行状况信息。                                   |
| `httptrace`        | 显示HTTP跟踪信息（默认情况下，最近100个HTTP请求-响应）。需要一个`HttpTraceRepository`组件。 |
| `info`             | 显示应用程序信息。                                           |
| `integrationgraph` | 显示Spring `integrationgraph` 。需要依赖`spring-integration-core`。 |
| `loggers`          | 显示和修改应用程序中日志的配置。                             |
| `liquibase`        | 显示已应用的所有Liquibase数据库迁移。需要一个或多个`Liquibase`组件。 |
| `metrics`          | 显示当前应用程序的“指标”信息。                               |
| `mappings`         | 显示所有`@RequestMapping`路径列表。                          |
| `scheduledtasks`   | 显示应用程序中的计划任务。                                   |
| `sessions`         | 允许从Spring Session支持的会话存储中检索和删除用户会话。需要使用Spring Session的基于Servlet的Web应用程序。 |
| `shutdown`         | 使应用程序正常关闭。默认禁用。                               |
| `startup`          | 显示由`ApplicationStartup`收集的启动步骤数据。需要使用`SpringApplication`进行配置`BufferingApplicationStartup`。 |
| `threaddump`       | 执行线程转储。                                               |





如果您的应用程序是Web应用程序（Spring MVC，Spring WebFlux或Jersey），则可以使用以下附加端点：

| ID           | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| `heapdump`   | 返回`hprof`堆转储文件。                                      |
| `jolokia`    | 通过HTTP暴露JMX bean（需要引入Jolokia，不适用于WebFlux）。需要引入依赖`jolokia-core`。 |
| `logfile`    | 返回日志文件的内容（如果已设置`logging.file.name`或`logging.file.path`属性）。支持使用HTTP`Range`标头来检索部分日志文件的内容。 |
| `prometheus` | 以Prometheus服务器可以抓取的格式公开指标。需要依赖`micrometer-registry-prometheus`。 |





最常用的Endpoint

- **Health：监控状况**
- **Metrics：运行时指标**
- **Loggers：日志记录**





### Health Endpoint

健康检查端点，我们一般用于在云平台，平台会定时的检查应用的健康状况，我们就需要Health Endpoint可以为平台返回当前应用的一系列组件健康状况的集合。

重要的几点：

- health endpoint返回的结果，应该是一系列健康检查后的一个汇总报告
- 很多的健康检查默认已经自动配置好了，比如：数据库、redis等
- 可以很容易的添加自定义的健康检查机制

![img](https://cdn.nlark.com/yuque/0/2020/png/1354552/1606908975702-4f9a3208-15ca-4a78-9f76-939ef986db7e.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_12%2Ctext_YXRndWlndS5jb20g5bCa56GF6LC3%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)

### Metrics Endpoint

提供详细的、层级的、**空间指标信息**，这些信息可以被pull（主动推送）或者push（被动获取）方式得到；

- 通过Metrics对接多种监控系统
- 简化核心Metrics开发
- 添加自定义Metrics或者扩展已有Metrics



![img](https://cdn.nlark.com/yuque/0/2020/png/1354552/1606909073222-c6e77ca3-4b1c-4f38-a1c6-8614dec4f7bc.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_16%2Ctext_YXRndWlndS5jb20g5bCa56GF6LC3%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)







### 管理Endpoints

#### 开启与禁用Endpoints

- 默认所有的Endpoint除过shutdown都是开启的。
- 需要开启或者禁用某个Endpoint。配置模式为  **management.endpoint.****<endpointName>****.enabled = true**

```yaml
management:
  endpoint:
    beans:
      enabled: true
```

- 或者禁用所有的Endpoint然后手动开启指定的Endpoint

```yaml
management:
  endpoints:
    enabled-by-default: false
  endpoint:
    beans:
      enabled: true
    health:
      enabled: true
```





#### 暴露Endpoints

支的暴露方式

- HTTP：默认只暴露**health**和**info** Endpoint
- **JMX**：默认暴露所有Endpoint
- 除过health和info，剩下的Endpoint都应该进行保护访问。如果引入SpringSecurity，则会默认配置安全访问规则

| ID                 | JMX  | Web  |
| ------------------ | ---- | ---- |
| `auditevents`      | Yes  | No   |
| `beans`            | Yes  | No   |
| `caches`           | Yes  | No   |
| `conditions`       | Yes  | No   |
| `configprops`      | Yes  | No   |
| `env`              | Yes  | No   |
| `flyway`           | Yes  | No   |
| `health`           | Yes  | Yes  |
| `heapdump`         | N/A  | No   |
| `httptrace`        | Yes  | No   |
| `info`             | Yes  | Yes  |
| `integrationgraph` | Yes  | No   |
| `jolokia`          | N/A  | No   |
| `logfile`          | N/A  | No   |
| `loggers`          | Yes  | No   |
| `liquibase`        | Yes  | No   |
| `metrics`          | Yes  | No   |
| `mappings`         | Yes  | No   |
| `prometheus`       | N/A  | No   |
| `scheduledtasks`   | Yes  | No   |
| `sessions`         | Yes  | No   |
| `shutdown`         | Yes  | No   |
| `startup`          | Yes  | No   |
| `threaddump`       | Yes  | No   |









#### 定制 Endpoint

##### 定制 Health 信息

```java
import org.springframework.boot.actuate.health.Health;
import org.springframework.boot.actuate.health.HealthIndicator;
import org.springframework.stereotype.Component;

@Component
public class MyHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        int errorCode = check(); // perform some specific health check
        if (errorCode != 0) {
            return Health.down().withDetail("Error Code", errorCode).build();
        }
        return Health.up().build();
    }

}

构建Health
Health build = Health.down()
                .withDetail("msg", "error service")
                .withDetail("code", "500")
                .withException(new RuntimeException())
                .build();
management:
    health:
      enabled: true
      show-details: always #总是显示详细信息。可显示每个模块的状态信息
```



```java
@Component
public class MyComHealthIndicator extends AbstractHealthIndicator {

    /**
     * 真实的检查方法
     * @param builder
     * @throws Exception
     */
    @Override
    protected void doHealthCheck(Health.Builder builder) throws Exception {
        //mongodb。  获取连接进行测试
        Map<String,Object> map = new HashMap<>();
        // 检查完成
        if(1 == 2){
//            builder.up(); //健康
            builder.status(Status.UP);
            map.put("count",1);
            map.put("ms",100);
        }else {
//            builder.down();
            builder.status(Status.OUT_OF_SERVICE);
            map.put("err","连接超时");
            map.put("ms",3000);
        }


        builder.withDetail("code",100)
                .withDetails(map);

    }
}
```

##### 定制info信息

常用两种方式

**编写配置文件**

```yaml
info:
  appName: boot-admin
  version: 2.0.1
  mavenProjectName: @project.artifactId@  #使用@@可以获取maven的pom文件值
  mavenProjectVersion: @project.version@
```

**编写InfoContributor**

```java
import java.util.Collections;

import org.springframework.boot.actuate.info.Info;
import org.springframework.boot.actuate.info.InfoContributor;
import org.springframework.stereotype.Component;

@Component
public class ExampleInfoContributor implements InfoContributor {

    @Override
    public void contribute(Info.Builder builder) {
        builder.withDetail("example",
                Collections.singletonMap("key", "value"));
    }

}
```





<http://localhost:8080/actuator/info> 会输出以上方式返回的所有info信息

##### 定制Metrics信息

**SpringBoot支持自动适配的Metrics**

- JVM metrics, report utilization of:

- - Various memory and buffer pools
  - Statistics related to garbage collection
  - Threads utilization
  - Number of classes loaded/unloaded

- CPU metrics
- File descriptor metrics
- Kafka consumer and producer metrics
- Log4j2 metrics: record the number of events logged to Log4j2 at each level
- Logback metrics: record the number of events logged to Logback at each level
- Uptime metrics: report a gauge for uptime and a fixed gauge representing the application’s absolute start time
- Tomcat metrics (`server.tomcat.mbeanregistry.enabled` must be set to `true` for all Tomcat metrics to be registered)
- [Spring Integration](https://docs.spring.io/spring-integration/docs/5.4.1/reference/html/system-management.html#micrometer-integration) metrics

**增加定制Metrics**

```java
class MyService{
    Counter counter;
    public MyService(MeterRegistry meterRegistry){
         counter = meterRegistry.counter("myservice.method.running.counter");
    }

    public void hello() {
        counter.increment();
    }
}


//也可以使用下面的方式
@Bean
MeterBinder queueSize(Queue queue) {
    return (registry) -> Gauge.builder("queueSize", queue::size).register(registry);
}
```





##### 定制Endpoint

```java
@Component
@Endpoint(id = "container")
public class DockerEndpoint {


    @ReadOperation
    public Map getDockerInfo(){
        return Collections.singletonMap("info","docker started...");
    }

    @WriteOperation
    private void restartDocker(){
        System.out.println("docker restarted....");
    }

}
```

场景：开发**ReadinessEndpoint**来管理程序是否就绪，或者**Liveness****Endpoint**来管理程序是否存活；

当然，这个也可以直接使用 <https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-kubernetes-probes>

# 原理解析

## Profile功能

为了方便多环境适配，springboot简化了profile功能。

### application-profile功能

`指定需要加载的配置文件`

- 默认配置文件  application.yaml；任何时候都会加载
- 指定环境配置文件  application-{env}.yaml
- 激活指定环境

- - 配置文件激活
  - 命令行激活：java -jar xxx.jar --**spring.profiles.active=prod  --person.name=haha**

- - - **修改配置文件的任意值，命令行优先**

- 默认配置与环境配置同时生效
- 同名配置项，profile配置优先





### @Profile条件装配功能

`指定容器中的组件在指定配置文件加载时才生效`

```java
@Configuration(proxyBeanMethods = false)
@Profile("production")
public class ProductionConfiguration {

    // ...

}
```

### profile分组

`可以同时加载多个配置文件`

```plain
spring.profiles.group.production[0]=proddb
spring.profiles.group.production[1]=prodmq

使用：--spring.profiles.active=production  激活
```



## 外部化配置

<https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config>



1. Default properties (specified by setting `SpringApplication.setDefaultProperties`).
2. `@PropertySource` annotations on your `@Configuration` classes. Please note that such property sources are not added to the `Environment` until the application context is being refreshed. This is too late to configure certain properties such as `logging.*` and `spring.main.*` which are read before refresh begins.
3. **Config data (such as** `**application.properties**` **files)**
4. A `RandomValuePropertySource` that has properties only in `random.*`.
5. OS environment variables.
6. Java System properties (`System.getProperties()`).
7. JNDI attributes from `java:comp/env`.
8. `ServletContext` init parameters.
9. `ServletConfig` init parameters.
10. Properties from `SPRING_APPLICATION_JSON` (inline JSON embedded in an environment variable or system property).
11. Command line arguments.
12. `properties` attribute on your tests. Available on `@SpringBootTest` and the [test annotations for testing a particular slice of your application](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-testing-spring-boot-applications-testing-autoconfigured-tests).
13. `@TestPropertySource` annotations on your tests.
14. [Devtools global settings properties](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools-globalsettings) in the `$HOME/.config/spring-boot` directory when devtools is active.



### 外部配置源

常用：**Java属性文件**、**YAML文件**、**环境变量**、**命令行参数**；



### 配置文件查找位置

(1) classpath 根路径

(2) classpath 根路径下config目录

(3) jar包当前目录

(4) jar包当前目录的config目录

(5) /config子目录的直接子目录

### 配置文件加载顺序：

1. 　当前jar包内部的application.properties和application.yml
2. 　当前jar包内部的application-{profile}.properties 和 application-{profile}.yml
3. 　引用的外部jar包的application.properties和application.yml
4. 　引用的外部jar包的application-{profile}.properties 和 application-{profile}.yml

### 指定环境优先，外部优先，后面的可以覆盖前面的同名配置项