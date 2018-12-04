# Springboot
* ##### @SpringBootApplication 告诉程序是一个springboot,是主配置类  
    该 @SpringBootApplication 注解等价于以默认属性使用 @Configuration ， @EnableAutoConfiguration 和 @ComponentScan 。
   ```
   SpringApplication.run(主程序类.class,args) //启动代码;
   ```
* ##### 导入这个插件,便可使用 java -jar 直接执行程序
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
* ##### @RestController  = @ResponseBody*@Controller
* ##### 嵌入式servlet容器(默认Tomcat)
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
* ##### application.yml ->  k:换行+空格v:值
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
* #####     @ConfigurationProperties(prefix = "person") //告诉springboot将本类中的所有属性和配置文件中相关的配置进行绑定 //prefix:告诉配置文件中那个下面的属性进行映射
* #####  可以对自己定义的参数进行提示
     ```
     <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-configuration-processor</artifactId>
     </dependency>     
     ```
* ##### @PropertySource("classpath:") 加载外部配置文件
* ##### @ImportResource(locations = {"classpath:"}) 导入spring配置文件,让其生效
* ##### --- 可以将yml文件进行分割
    ```
        spring:                 -> --- 分割  ->     spring:
                profiles:                                  profiles:  当前模块激活名字
                    active: 激活的名字
    ```
* ##### 文件优先顺序  file/config> file > file/resources/config > file/resources/  初始的配置级别最低
* ##### 可以使用命令行的方式修改springboot配置  java -jar hellospringboot-1.0.0.0.jar --server.port=8087
* ##### debug= true 可以让看那些自动配置类生效
* ##### 以后导入其他框架包,需要排除自带的日志包
* ##### 日志开启
     ```
            logging:     // trace<debug<info<warn<error   logging:    file|path 只能用一个
                level:   //日志级别                           file: springboot.log  //选择生成日志文件的位置及名字
                                                             path: log/            //选择哪个文件夹下生成spring.log
                    
                    -》全局变量 Logger logger = LoggerFactory.getLogger(getClass());
                                -> logger.(trace|debug|info|warn|error)("日志信息");
                                               active: 激活的名字
     ```
* ##### 若想更换网页图标,在静态资源文件夹下放置一个 favicon.ico的图标
* ##### Thymeleaf ->引入spring-boot-starter-thymeleaf  
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
* ##### 国际化
    1. 可以以 在i18n文件夹下创建  (标题_zh_CH.properties,标题_en_US.properties,标题.properties 等国际化配置文件)
    2. 配置文件中Resource Bundle视图模式下进行编辑
    3. 配置文件中 spring.messages.basename=i18n.标题
    4. th:text="#{}"
* #####  Restful
    1. @DeleteMapping()//删除 @PutMapping()//修改 @PostMapping()//添加 @GetMapping()//查询 这种风格的代替RequestMapping
    2. ` <input type="hidden" name="_method" value="put|delete"/>`
* #####  修改日期格式   spring.mvc.date-format=yyyy-MM-dd
* #####  <mvc:view-controller path="请求路径" view-name="映射到那个文件"/>
* #####  注解版错误处理
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
* ##### 注解版拦截器
      * 继承WebMvcConfigurerAdapter
      * 需要注入自定义的拦截器
      * 重写addInterceptors方法
      * 添加拦截器并且设置拦截路径
      * 代码
          ```
          public class ConfigBean extends WebMvcConfigurerAdapter{  
              @Autowired
              private TimerInterceptor timerInterceptor;
          
              @Override
              public void addInterceptors(InterceptorRegistry registry) {
                  registry.addInterceptor(timerInterceptor).addPathPatterns("/**").excludePathPatterns("/hello");
              }
          }
          ```
* ##### 注解版AOP
      * 自定义类 @Aspect @Component 标注为aop类并注入
      * 构写返回Object方法 @Around(execution表达式)  //@before @after等
      * 方法传入参数 ProceedingJoinPoint
          * proceedingJoinPoint.getArgs();//获得切点方法的所有参数
          * proceedingJoinPoint.proceed();//执行方法后
      * 返回 执行方法得到的object
      ```
      @Aspect
      @Component
      public class TestAspect {
      
          @Around("execution(* com.my.security.DemoApplication.*(..))")
          public Object Testaop(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
              Object[] args = proceedingJoinPoint.getArgs(); //获得切点方法的所有参数
              Arrays.stream(args).forEach(e-> System.out.println(e));
              Object object = proceedingJoinPoint.proceed();//执行方法后
              return object;
          }
      }
      ```
* ##### JDBC
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
* ##### mybatis
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
* ##### 事务处理 主文件 @EnableTransactionManagement  便可启动@Transactional
* ##### jpa
 ```
    jpa:
      hibernate:
          ddl-auto: update
      show-sql: true
 ```
* ##### @RequestMapping(value="/user/{id:正则表达式}") 可以接受特定的值类型
* ##### 数据验证
      * 在实体类上加入验证性注解
          ```
              @NotBlank //字符串不能为空   
              @NotEmpty //字符串和集合不能为空
              @Pattern(regex="正则表达式")
              @Email //必须是电子邮件
              @Range(min= ,max= ) //数字大小
              @URL //合法的URL
              上面所有注解都有一个属性 message="自定义错误消息"
          ```
      * 在方法参数前@valid 开启验证
      * 传入参数BindingResult errors)来存取错误信息
          ```
              errors.hasErrors() //判断是否有错误信息
              errors.getAllErrors() //拿取全部错误信息
          ```
* ##### 多线程处理  
      > 当一个请求进入Tomcat时,会用主线程进行处理,而使用这种多线程,便可以提高吞吐量  
      ![多线程](https://github.com/SuperCourierYangyufan/notes/blob/master/img/%E4%BD%BF%E7%94%A8%E5%A4%9A%E7%BA%BF%E7%A8%8B%E5%A4%84%E7%90%86%E8%BF%87%E7%A8%8B.PNG)
      * 代码
      ```
       public Callable<String> async() throws Exception{
              Callable<String> callable = new Callable<String>() {
                  @Override
                  public String call() throws Exception {
                      //子线程代码
                      return "success"; //正常返回
                  }
              };
              return callable;
          }
      ```
* ##### 文档说明swagger2
      * POM
      ```
                   <!--生成文档-->
                  <dependency>
                      <groupId>io.springfox</groupId>
                      <artifactId>springfox-swagger2</artifactId>
                      <version>2.7.0</version>
                  </dependency>
                  <dependency>
                      <groupId>io.springfox</groupId>
                      <artifactId>springfox-swagger-ui</artifactId>
                      <version>2.7.0</version>
                  </dependency>
      ```
      * 启动类 @EnableSwagger2
      * 注解,对于方法,参数,实体类属性说明
      ```
              @ApiParam(value = "参数说明")
              @ApiModelProperty(value = "属性说明")
              @ApiOperation(value = "方法说明")
      ```
      * http://localhost:8080/swagger-ui.html
* ##### cache缓存
    * 简单使用
        * 启动类@EnableCaching
        * 基本注解
            1. @Cacheable()  开启缓存
                * value="指定缓存组件名字" 必须,可以为数组
                * key="缓存数据的key(#{id})"  支持spel
                * keyGenerator="key的生成方式" 不能与key共存
                * cacheManager="指定用哪个缓存管理器"
                * condition="指定条件缓存(#id>3)" 
                * unless="指定条件不缓存"
                * sync="是否异步"  unless失效
            2. @CacheEvict() 清空缓存
                * value必须
                * key指定删除哪一个
                * allEntries=true 删除全部,不用指定key了
                * beforeInvocation=true 方法执行之前清空
            3. @CachePut(value,key)  调用方法并更新缓存
                * value,key必须
                * value,key应该与Cacheable()里的一致,#resultF.id
    * Redis
        1. 导入包spring-boot-starter-data-redis
        2. yml中 spring.redis.host= 主机名
        3. 直接可以使用RedisTemplate对象
            * redisTemplate.opsForValue();//操作字符串
            * redisTemplate.opsForHash();//操作hash
            * redisTemplate.opsForList();//操作list
            * redisTemplate.opsForSet();//操作set
            * redisTemplate.opsForZSet();//操作有序set
        4. 保存对象需要对象进行序列化
        > 在使用缓存便自动使用redis,注意实体类要序列化
* ##### 消息队列
    1. 导入包spring-boot-starter-amqp
    2. YML
    ```
        spring:
          rabbitmq:
            host: 193.112.172.104
            username: guest
            password: guest
            port: 5672
    ```
    3. 进入网页将交换器绑定队列|AmqpAdmin代码中进行操作
        * 注入AmqpAdmin
        * 创建交换器exchanges
        * 测试方法中 创建交换器 队列 绑定
        ```
            @Autowired
            	private AmqpAdmin amqpAdmin;
            	@Test
            	public void createExchanges(){
            		amqpAdmin.declareExchange(new FanoutExchange("交换器名字")); //若交换器为单点模式应为DirectExchange("交换名字"));
            		amqpAdmin.declareQueue(new Queue("队列名字",true));
            		amqpAdmin.declareBinding(new Binding("队列名字",Binding.DestinationType.QUEUE,"交换器名字","队列名字",null));
            	}
        ```
    4. @EnableRabbit在启动类上
    5. RabbitTemplate进行操作
        * rabbitTemplate.convertAndSend("交换器名字","队列名字",发送对象); //通过交换器发送该对象到队列中
        * Object o = rabbitTemplate.receiveAndConvert("队列名字");  //获得队列的数据
    6. @RabbitListener(queues = {"队列名字"})进行监听消息
        * 方法参数为Message,通过message.getBody()可以获取数据或其他内容(推荐)
        * 方法参数为发送对象的名字便也获取数据
* ##### 任务处理
    1. 异步任务
        * @EnableAsync 在启动类上
        * @Async在方法上开启异步任务  
    2. 定时任务呀
        * @EnableScheduling在启动类上
        * @Scheduled(cron表达式)
            * 6位,以空格分割
            * cron表达式 ![cron表达式](https://github.com/SuperCourierYangyufan/notes/blob/master/img/cron%E8%A1%A8%E8%BE%BE%E5%BC%8F.PNG)
    3. 邮件任务
        * 加入spring-boot-starter-mail依赖
        * YML
            ```
                spring:
                  mail:
                    username: yangyufan199691@163.com
                    password: Yang199691
                    host: smtp.163.com
                    properties:
                      mail:
                        smtp:
                          ssl:
                            enable: true
                # qq邮箱才需要properties
            ```      
        * 代码
            ```
                @Autowired
                	private JavaMailSender javaMailSender;
                	@Test
                    	public void test02() throws Exception{
                            MimeMessage message = javaMailSender.createMimeMessage();
                            MimeMessageHelper helper = new MimeMessageHelper(message, true); //true代表要发送文件
                            helper.setText("<H1>这是内容</H1>",true);//true代码会转为html
                            helper.setSubject("标题");
                            helper.setFrom("yangyufan199691@163.com");
                            helper.setTo("18827421758@163.com");
                            helper.addAttachment("1.jpg",new File("C:\\Users\\Administrator\\Desktop\\head.jpg"));
                    	    javaMailSender.send(message);
                    	}
            ```
* ##### Dubbo
    1.  生产者
        * 引入依赖
        ```
                <dependency>
                    <groupId>com.alibaba.boot</groupId>
                    <artifactId>dubbo-spring-boot-starter</artifactId>
                    <version>0.1.0</version>
                </dependency>
                <dependency>
                    <groupId>com.github.sgroschupf</groupId>
                    <artifactId>zkclient</artifactId>
                    <version>0.1</version>
                </dependency>
        ```
        * YML
        ```
            dubbo:
              application:
                name: demo-producer
              registry:
                address: zookeeper://193.112.172.104:2181
              scan:
                base-packages: com.my.demo6.service
        ```
        * 实现类Impl(必须为实现类得有接口父类)
        ```
            @Component
            @Service //dubbo下的
        ```
    2. 消费者
        * 依赖同样
        * yml只需要name,address
        * 将生产者的接口类全部放在消费者这里,要求路径一样
        * 引用@Reference,(duboo的实现),放在引用类上
* ##### 热部署
    * 依赖
        ```
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-devtools</artifactId>
                <optional>true</optional>
                <version>1.5.14.RELEASE</version>
            </dependency>
        ```
    * ctrl+F9 后便可以修改
* ##### 监控管理
    * 依赖sprng-boo-stater-actuator
    * YML
            `managment.sercurity.enabled=fales`
    * 监控路径![监控路径](https://github.com/SuperCourierYangyufan/notes/blob/master/img/%E7%9B%91%E6%8E%A7.PNG)
* ##### 自动装配知识点
	* 组件自动装配流程
		1. 激活: @EnableAutoConfiguration
		2. 实现: XXXAutoConfiguration
		3. 配置: /META-INF/spring.factories
    * 自定义条件装配
        1. 基于配置方式实现 @Profile //当传入相应的参数,执行相应的方法
        2. 基于编程方式实现 @Conditional //传入一个匹配对象,该对象会继承Condition,重写方法,获得注解内属性进行比较,true才执行@Conditional的方法
    * @enable模块是是指具备相同领域的功能组件集合
    * @componentScan指定扫描那些包开启注解
    * @Configuration等于@component,来代替XML注册

* ##### 使用原始servlet
    * 基本原生使用
        1. 在启动类同级目录下建立包
        2. 创建类
        3. 类注解@WebServlet(urlPatterns="映射路径")
        4. 类继承HttpServlet,并重写doGet,doPost方法
        5. 在启动类上@ServletComponentScan(basePackages = "servlet类路径")
    * 异步servlet
        1. @webServlet(...,asyncSupported = true) //开启异步
        2. 代码部分
            ``` 
            AsyncContext async = req.startAsync();
                async.start(()->{
                    try {
                     //异步代码
                     //触发完成
                     async.complete();
                    } catch (IOException e) {
                      e.printStackTrace();
                    }
            });
             ```
        
            