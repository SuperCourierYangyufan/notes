#SpringCloud
1. #####dubbo基于rpc远程过程调用,cloud基于RestFul调用 (通信机制的区别)
2. #####maven导入lombok
    ```
       @AllArgsConstructor  全参构造
       @NoArgsConstructor   无参构造
       @Data                get set toString
       @Accessors(chain = true)  链式 user.setName().setAge()... 
    ```
3. #####创建配置类   @Configuration     <-restful风格开发
    ```
        @Bean
        public RestTemplate getRestTemplate(){
            return new RestTemplate();
        }
    ```  
     controller层->return restTemplate.(post|get)ForObject("服务端地址","携带参数","返回参数类型.class")  

4.  #####Eureka,类似于Zookeeper,注册中心
    *  Eureka遵守AP(可用性)  Zookeeper遵守CP(一致性)    p:分区容错  
    *  **【注册中心】**
        * POM  
        ```
        <artifactId>spring-cloud-starter-eureka-server</artifactId>
        ```
        * YML
        ```
              server:
                port: 7001
              eureka:
                instance:
                  hostname: localhost  #实例名称(IP或者域名)
                client:
                  register-with-eureka: false #不自己注册自己
                  fetch-registry: false #eureka不会被检索
                  service-url:
                    defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/ #设置eureka Server交互的地址查询服务和注册服务
                        ->集群情况下将defaultZone:以,分割,分别写其他注册地址
        ```
        * 启动类上@EnableEurekaServer
    * **【客户端】**
        * POM
        ```
               <artifactId>spring-cloud-starter-eureka</artifactId>  // 将微服务provider侧注册进eureka
               <artifactId>spring-cloud-starter-config</artifactId>
               <artifactId>spring-boot-starter-actuator</artifactId> //需要对服务进行详细说明导入,监控信息的完善
        ```   
        * YML
        ```
             eureka:
                client:
                  service-url:
                    defaultZone: http://localhost:7001/eureka (集群下,写全部的注册地址)
                instance:
                  instance-id: xxxxxxxxxxx //status名称
                  prefer-ip-address: true  //显示ip地址
               info:
                 自定义key: 自定义value   //可多个，详细说明
        ```
        * @EnableEurekaClient
        * 注册后暴露名字就是spring.application.name的值(PS.别带下划线)
5.  #####Ribbon是一个客户端负载均衡的工具   //均是在消费者方操作
    * **【服务端】**
        * POM
        ```
                   <artifactId>spring-cloud-starter-eureka</artifactId>
                   <artifactId>spring-cloud-starter-ribbon</artifactId>
                   <artifactId>spring-cloud-starter-config</artifactId> 
        ```
        * YML
        ```
                 eureka:
                    client:
                      service-url:
                        defaultZone: http://localhost:7001/eureka,.......
                      register-with-eureka: false
        ```
        * 在configuration类中对注册的RestTemplate上加上@LoadBalanced
            * 默认没有选择算法,便是轮询。若要选择算法,则在@configuration类中,注入算法类(IRule类)
        
        * 在启动类上@EnableEurekaClient
        * 对restTemplate调用方法,传入的第一个参数URL,前缀改为private static final String 名字 = "http://注册到eureka中的名字"
6.  #####Feign是以一个接口加注解的服务端的Web服务客户端.
    * POM
        ```
                  <artifactId>spring-cloud-starter-feign</artifactId>(公共模块与服务端均要)
        ```  
    * 用法
        * 一.在公共模块中,创建于service层名字对应的接口(UserService->UserClientService)
        * 二.接口上@FeignClient(value = "该客户端注入eureka中的名字")
        * 三.写对应的客户端(生产者)Controller层中的方法不加方法体
            ```
                    @RequestMapping(value = "/dept/get/{id}",method = RequestMethod.GET)
                    public Dept get(@PathVariable(value = “id”) Integer id);  一定要写(value = “id”)
            ```
        * 四.在服务端注入该接口,并写方法进行调用与返回
        * 五.在主启动类上
             ```
                   @EnableFeignClients(basePackages = {"com.my.springcloud"})
                    @ComponentScan("com.my.springcloud") 
             ```
7. #####Hystrix熔断器【客户端】
    * POM
    ```
    <artifactId>spring-cloud-starter-hystrix</artifactId>
    ```
    * YML
    ```
        instance-id: xxxxxxxxxxx+Hystrix //服务名称修改,可选
    ```
    * 代码操作
        ```
            @GetMapping("/dept/get/{id}")
            @HystrixCommand(fallbackMethod = "finByIderror")  //异常后找那个方法
            public  Dept findById(@PathVariable Integer id){
                Dept dept = deptService.findById(id);
                if(dept == null){ //出现异常,id不存在
                    throw new RuntimeException(); //抛出异常
                }
                return dept;
            }
            public Dept finByIderror(@PathVariable Integer id){ //熔断后调用的方法
                return new Dept().setDeptno(Long.valueOf(id)).setDname("该"+id+"没有对应的信息,null--@HystrixCommand")
                        .setDb_source("null DataBase");
            }
        ```
    * 主启动类 @EnableHystrix
    * 服务降级:整体资源不够,先关闭某部分服务,待度过难关后,再开启回来**将熔断方法与正常方法分离**
        * 在公共模块中创建类 自定义名字ClientServiceFallBack 继承 FallbackFactory<Feign的client接口>
        * 注入@Component
        * 实现create()方法   return Feign的client接口(){..................里面的方法便是熔断方法}
        * 在自定义名字ClientService方法上修改为  
            `@FeignClient(value = "dept8081",fallbackFactory = 自定义名字ClientServiceFallBack类.class )`
        * 消费者端yml
            ```
            feign:
              hystrix:
                enabled: true
            ```
        * 代码
            ```
                [自定义名字ClientServiceFallBack]
                @Component //1.注入
                public class DeptClientServiceFallBack implements FallbackFactory<DeptClientService> { //2.继承关系
                    @Override
                    public DeptClientService create(Throwable throwable) { //3.实现方法
                        return new DeptClientService() { //4.熔断方法类
                        .......
                            @Override
                            public Dept findById(Integer id) { //5.熔断方法
                                return  new Dept().setDeptno(Long.valueOf(id)).setDname("服务以关闭")
                                        .setDb_source("null DataBase");
                            }
                        .......
                        };
                    }
                }
                
               [自定义名字ClientService] 
               @FeignClient(value = "dept8081",fallbackFactory = DeptClientServiceFallBack.class ) //6.提供熔断
               public interface DeptClientService {
                ......................
               }
            ```
    * Hystrix Dashboard 监控
        【新监控模块】
        * POM
        ```
            <artifactId>spring-cloud-starter-hystrix</artifactId>
            <artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
        ```   
        * yml  
             只需要端口号
        * 主启动类  
            @EnableHystrixDashboard
        -----------------------------------------------------------------------
        【客户端】
        * POM    
        ```
         <artifactId>spring-boot-starter-actuator</artifactId>
        ```
        【网页】
        http://ip:自定端口/hystrix 进入
        输入: http://客户端ip:端口/hystrix.stream 进行监控
8. #####Zuul路由网关 提供=代理+路由+过滤 统一访问的入口  
    【新路由模块】
    * POM
    ```
                <!-- zuul路由网关 -->
                    <artifactId>spring-cloud-starter-zuul</artifactId>
                    <artifactId>spring-cloud-starter-eureka</artifactId>
                <!-- actuator监控 -->
                    <artifactId>spring-boot-starter-actuator</artifactId>
                <!-- hystrix容错 -->
                    <artifactId>spring-cloud-starter-hystrix</artifactId>
                    <artifactId>spring-cloud-starter-config</artifactId>
    ```
    * YML
    ```
    server:
      port: 9527
    spring:
      application:
        name: demo-zuul
    eureka:
      client:
        service-url:
          defaultZone: http://local:7001/eureka/
      instance:
        instance-id: demo-zuul
        prefer-ip-address: true
    ```
    * 主启动类加上@EnableZuulProxy
    * 访问   http://路由网关id:端口/注册eureka名字(注意大小写)/访问路径
    * 映射规则
        * YML
        ```
            zuul:
              routes:
                myDept.serviceId: dept8081  #真名
                myDept.path: /mydept/**     #映射名
              ignored-services: dept8081 | "*"    #取消真名访问
              prefix: /com                        #前缀
        ```
        * 访问  http://路由网关id:端口/mydept/访问路径
9. #####分布式配置中心SpringCloud Config
    【Git】
    * clone一个新的仓库
    * YML并上传
    ```
        spring:
            profiles:
                active:
                - dev
        ---
        spring:
            profiles: dev #开发环境
            application:
                name: demo-config-dev
        ---
        spring:
            profiles: test #测试环境
            application:
                name: demo-config-test
        #UTF-8
    ```
    【config项目】
     * POM
     ```
        <artifactId>spring-cloud-config-server</artifactId>
        hystrix,eureka...
     ```
     * YML
     ```
     server:
       port: 3344
     spring:
       application:
         name: demo-config
       cloud:
         config:
           server:
             git:
               uri: git@github.com:SuperCourierYangyufan/demo-config.git #github地址
     ```
     * 主启动类@EnableConfigServer  
     
     【网页读取】
     * http://localhost:3344/application-(dev|test).yml
     
     【客户端】
    * pom 
        ```
        <artifactId>spring-cloud-starter-config</artifactId>
        ```
    * bootstrap.yml是系统级,优先级最高,新增
    ```
        spring:
          cloud:
            name: demo #需要从git上读取资源的名称,没有yml
            profile: dev #本次访问的配置项
            label: master
            uri: http://localhost:3344
    ```
    * application.yml只需要spring.application.name:与上面保持一致即可
