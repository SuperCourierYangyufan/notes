#Springboot
* #####@SpringBootApplication 告诉程序是一个springboot,是主配置类  
    该 @SpringBootApplication 注解等价于以默认属性使用 @Configuration ， @EnableAutoConfiguration 和 @ComponentScan 。
   ```
   SpringApplication.run(主程序类.class,args) //启动代码;
   ```
* #####导入这个插件,便可使用 java -jar 直接执行程序
```
    <build>
           <plugins>
                  <plugin>
                       <groupId>org.springframework.boot</groupId>
                       <artifactId>spring-boot-maven-plugin</artifactId>
                   </plugin>
               </plugins>
       </build>
```
* #####@RestController  = @ResponseBody*@Controller
* #####嵌入式servlet容器(默认Tomcat)
  1. server.port=8080  修改tomcat的端口号
  2. 以war包的方式进行项目创建可以使用外部tomcat,创建目录结构,便可以使用jsp
  3. 支持Jetty(适用长连接)  Undertow(不支持JSP)
      * 排除spring-boot-start-tomcat依赖
      * 引入其他容器的依赖
  4. 使用原生servlet,Filter,Listener
     ```
        <bean class="org.springframework.boot.web.servlet.ServletRegistrationBean">   //property注入页OJBK
                <constructor-arg name="servlet" ref="myServlet"/>     //该类需要继承HttpServlet 的doGet|doPost方法 //需要注入
                <constructor-arg name="urlMappings" value="/servlet"/> //访问路径
       </bean>
     ```
     ```
        ><bean class="org.springframework.boot.web.servlet.FilterRegistrationBean">
                             <property name="filter" ref="myFilter"/>   //需要实现Filter,并注入
                             <property name="urlPatterns" >
                                 <list>                                 //多个拦截路径
                                     <value>/servlet</value>
                                     <value>/success</value>
                                 </list>
                             </property>
         </bean>
     ```
     ```
        <bean class="org.springframework.boot.web.servlet.ServletListenerRegistrationBean">
                              <property name="listener" ref="myLintener"/>    //需要实现相应监听的接口(ServletContextListener),并注入
        </bean>
     ```
* #####application.yml ->  k:换行+空格v:值
    1. 普通值(number,String,boolean) 直接写,无需引号。 若带"" 里面的内容不会进行转义字符  若是'' 则会进行转义
    2. 对象或Map
        ``` 
            User:                    ->       User:(空格){Username:(空格)abc,age:(空格)18}
               Username:(空格)abc
               age:(空格)18
        ```
    3. List数组等
        ```
            pets:               ->     pets:(空格)[cat,dog]
                -(空格)cat
                -(空格)dog
        ```
    4. 可以使用占位符 ${}  里面可以写 random. 产生随机数|用前面的属性名占位,用:进行默认赋值-> dog: ${User.name:(没有空格)杨宇帆}
* #####    @ConfigurationProperties(prefix = "person") //告诉springboot将本类中的所有属性和配置文件中相关的配置进行绑定 //prefix:告诉配置文件中那个下面的属性进行映射
* ##### 可以对自己定义的参数进行提示
     ```
     <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-configuration-processor</artifactId>
     </dependency>     
     ```
* #####@PropertySource("classpath:") 加载外部配置文件
* #####@ImportResource(locations = {"classpath:"}) 导入spring配置文件,让其生效
* #####--- 可以将yml文件进行分割
    ```
        spring:                 -> --- 分割  ->     spring:
                profiles:                                  profiles:  当前模块激活名字
                    active: 激活的名字
    ```
* #####文件优先顺序  file/config> file > file/resources/config > file/resources/  初始的配置级别最低
* #####可以使用命令行的方式修改springboot配置  java -jar hellospringboot-1.0.0.0.jar --server.port=8087
* #####debug= true 可以让看那些自动配置类生效
* #####以后导入其他框架包,需要排除自带的日志包
* #####日志开启
     ```
            logging:     // trace<debug<info<warn<error   logging:    file|path 只能用一个
                level:   //日志级别                           file: springboot.log  //选择生成日志文件的位置及名字
                    root(可以选择哪个文件的日志级别): trace       path: log/            //选择哪个文件夹下生成spring.log
                    
                    -》全局变量 Logger logger = LoggerFactory.getLogger(getClass());
                                -> logger.(trace|debug|info|warn|error)("日志信息");
                                               active: 激活的名字
     ```
* #####若想更换网页图标,在静态资源文件夹下放置一个 favicon.ico的图标
* #####Thymeleaf ->引入spring-boot-starter-thymeleaf  
    1. (更换3.0版本)
    ``` 
         <thymeleaf.version>3.0.9.RELEASE</thymeleaf.version>  
         <thymeleaf-layout-dialect.version>2.2.2</thymeleaf-layout-dialect.version>
    ```
    2. 默认前置位templates文件夹  默认后缀为.html (springmvc返回)
    3. 导入名称空间  `<html lang="en" xmlns:th="http://www.thymeleaf.org">`
    4. th:替换属性(text|id|class....)  ->   [[${行内,跟el一样了}]]  |  一个[]就不会进行转义了  ps:自结束标签不能用th,如input
    5. 表达式 ${(OGNL|内置属性)}
    6. th:object=${session.user}得到一个类  可以通过 *{name},*{age}直接获取里面的值  == ${session.user.name}
    7. @{/路径/路径(参数名=${},参数名='你好')} 填写地址    若需要在中使用变量${} 应使用拼串的方式
    8. 可以将标签上设置一个标签没有的属性 th:attr="属性名=属性值(delete_url=@{/Item/}+${user.id})"; (button)  
        * jQ中  $("#myform").attr("action",$(this).attr("delete_url")).submit();    attr("元素的属性","给予这个属性赋值")
    9.  th:each="每次遍历的名字 : ${遍历的属性}"   遍历在那个标签上,标签就会重复出现循环次数
    10. spring.thymeleaf.cache=false    禁用掉缓存      (Ctrl+F9)页面修改完重新编译
    11. th:if="判断条件"
    12. th:fragment="设置模板名"     th:(insert|replace|include)="~{根据springmvc前后缀解析前的地址页面::模板名}"
        * insert:将模板整个插入到div中  replace:将div替换成片段   include:仅将模板中的内容插入div中
        * th:(insert|replace|include)="~{根据springmvc前后缀解析前的地址页面::选择器(#id)}" //不需要设置模板了
        * 可以在~{}中模板名或选择器后加(),里面可以传入参数.导入的页面便会携带参数
    13. ![语法格式](https://github.com/SuperCourierYangyufan/notes/blob/master/img/thymeleaf%E8%AF%AD%E6%B3%95.png)
* #####国际化
    1. 可以以 在i18n文件夹下创建  (标题_zh_CH.properties,标题_en_US.properties,标题.properties 等国际化配置文件)
    2. 配置文件中Resource Bundle视图模式下进行编辑
    3. 配置文件中 spring.messages.basename=i18n.标题
    4. th:text="#{}"
* ##### Restful
    1. @DeleteMapping()//删除 @PutMapping()//修改 @PostMapping()//添加 @GetMapping()//查询 这种风格的代替RequestMapping
    2. ` <input type="hidden" name="_method" value="put|delete"/>`
* ##### 修改日期格式   spring.mvc.date-format=yyyy-MM-dd
* ##### <mvc:view-controller path="请求路径" view-name="映射到那个文件"/>
* ##### 错误处理
    1. 在模板文件夹下创建error文件夹,下面可以放置404.html,500.html(4xx.html,5xx.html),当放生响应错误的时候,便会自动跳转
    2. 错误页面可以取的属性   ->    timestamp:时间戳 status:状态码 error:错误提示 exception:异常对象 message:异常消息 errors:JSR303数据校验错误
    3. 异常处理类 == HandlerExceptionResolver
        * 在类上标注@ControllerAdvice
        * 在方法上标注@ExceptionHandler("异常类.class")
        * 方法与普通controller方法一致,可通过参数 Exception e 获得信息
        * 这种方式可以自定义springboot错误时发送的json数据
            * 需要Map存入json(JSON的每一类数据放入一次)
            * request.setAttribute("javax.servlet.error.status_code",400)
            * return:"forward:/error"
* #####JDBC
    1. 数据库基本配置
    ```
       spring.datasource.username=root
       spring.datasource.password=admin
       spring.datasource.url=jdbc:mysql://localhost:3306/jdbc
       pring.datasource.driver-class-name=com.mysql.jdbc.Driver 
    ```
    2. 在resources下放置schema.sql文件会在项目启动时运行(建表语句),data.sql(数据语句)
    3. 指定sql文件名和路径  -> spring.datasource.schema=classpath:schema-all.sql
    4. (@Autowired)jdbcTemplate.update(增删改)  query系列查询
    5. 导入常量池后 spring.datasource.type=com.alibaba.druid.pool.DruidDataSource 引入
        ```
            dbcp2:
              min-idle: 5  //数据库连接池最小维持连接数
              initial-size: 5 //初始化连接数
              max-total: 5    //最大连接数
              max-wait-millis: 200   //等待连接获取最大超时时间
        ```
* #####mybatis
    1. 注解版
        * 在同名实体类接口上@mapper  在符合规则的方法上@(insert|update|delete|query)
        * @Options(useGeneratedKeys = true,keyProperty = "id") 获取自增主键
        * @MapperScan(value="批量扫描mapper的接口的包路径")  //就不需要@mapper
    2. 配置版
        * 在同名实体类接口上@mapper
        * YML
            ```
                mybatis:
                    mapper-locations: classpath:mybatis/mapper/*.xml        //mapper映射文件
                    config-location: classpath:mybatis/mybatis-config.xml   //mapper全局映射文件
                    type-aliases-package: com.my.springcloud.entities       //告诉实体类位置
            ```
* #####事务处理 主文件 @EnableTransactionManagement  便可启动@Transactional
* #####jpa
 ```
    jpa:
      hibernate:
          ddl-auto: update
      show-sql: true
 ```
