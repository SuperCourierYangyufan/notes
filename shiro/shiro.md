# Shiro

### 配置版
1. URL授权采用第一次匹配优先的方法,所以同样的路径,上面的会执行,而下面不会
2. @RequiresRoles({"admin"}) 必须有admin权限才可以使用该代码块
    * 注意需要对没有权限访问时的异常进行处理
    * 注意最后不要放在service层,因为不能与事务同时处理
3. SecurityUtils.getSubject().getSession();得到session
4. 设置记住我属性为true,在xml中为user的url在下次未登入时可以访问



* realm代码
    ```
        public class ShiroRealm extends AuthorizingRealm {
        
            @Override //认证
            protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) {
                //将其转化为UsernamePassWordToken
                UsernamePasswordToken token = (UsernamePasswordToken)authenticationToken;
                //获得账号密码
                String username = token.getUsername();
                String password = new String(token.getPassword());
                //从数据库中获取数据
                //根据需求判断,抛出异常,可自定义
                if(username.equals("hello")){
                    throw new UnknownAccountException("hello");
                }
                //判断用户名密码正确
                SecurityUtils.getSubject().getSession().setAttribute("user","杨宇帆"); //存入用户至session
                //以下数据从数据库读取
                Object principal = username; //用户实体类,或者或户名
                Object credentials = "E10ADC3949BA59ABBE56E057F20F883E"; //数据库密码 加密
                ByteSource byteSource =ByteSource.Util.bytes(username); //盐,以用户账号进行加盐
                String realmName = getName(); //当前realm的名字
                SimpleAuthenticationInfo info = new SimpleAuthenticationInfo(principal,credentials,realmName);
        //        Object result = new SimpleHash(username, credentials, byteSource, 5);//最终密码,注册时用这种方法存入密码 5为加密次数
        //        SimpleAuthenticationInfo info = new SimpleAuthenticationInfo(principal,credentials,byteSource,realmName);
                return info;
            }
        
            @Override //授权
            protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
                //从中获取登录用户的信息
                Object principal = principalCollection.getPrimaryPrincipal(); //获得用户名,或者是用户实体类
                //利用登入的用户信息来对当前用户角色进行授权
                Set<String> roles = new HashSet<>(); //权限列表
                //从数据库获得权限级别
                //添加权限
                roles.add("index");
                if(principal.equals("admin")){
                    roles.add("admin");
                }
                //创建SimpleAuthorizationInfo,添加权限列表进去
                SimpleAuthorizationInfo info = new SimpleAuthorizationInfo(roles);
                return info;
            }
        }
    ```
* controller层代码
    ```
         @RequestMapping("/shiroLogin")
            public String login(String username, String password, HttpServletRequest request){
                Subject subject = SecurityUtils.getSubject();
                if(!subject.isAuthenticated()){
                    UsernamePasswordToken token = new UsernamePasswordToken(username, password);
                    token.setRememberMe(true); //设置记住我
                    try {
                        subject.login(token); //传入realm
                    }catch (AuthenticationException e){
                        request.setAttribute("error",e.getMessage());
                        return "forward:"+request.getContextPath()+"/login.jsp"; //失败转发到登入页面
                    }
                }
                return "redirect:/success.jsp"; //成功
            }
    ```
    
### springboot
```
    import com.my.myshiro.shiro.ShiroRealm;
    import org.apache.shiro.authc.credential.HashedCredentialsMatcher;
    import org.apache.shiro.mgt.SecurityManager;
    import org.apache.shiro.spring.LifecycleBeanPostProcessor;
    import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
    import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
    import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
    import org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator;
    import org.springframework.beans.factory.annotation.Qualifier;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.context.annotation.DependsOn;
    
    import java.util.LinkedHashMap;
    import java.util.Map;
    
    /**
         配置哪些页面需要受保护,以及访问这些页面需要的权限.
         格式:路径 = 拦截器[参数]
         【拦截器】
         1). anon 可以被匿名访问
         2). authc 必须认证(即登录)后才可能访问的页面.
         3). logout 登出.    退出url
         4). roles 角色过滤器   roles[权限名]
         5). user 认证或记住我均可以访问
     */
    @Configuration
    public class ShiroConfig {
        @Bean("shiroFilter")
        public ShiroFilterFactoryBean shiroFilter(@Qualifier("securityManager") SecurityManager manager) {
            ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
    
            // 必须设置 SecurityManager
            shiroFilterFactoryBean.setSecurityManager(manager);
    
            // 如果不设置默认会自动寻找Web工程根目录下的"/login"页面
            shiroFilterFactoryBean.setLoginUrl("/login.html");
            // 登录成功后要跳转的链接
            shiroFilterFactoryBean.setSuccessUrl("/index.html");
            // 未授权界面;
            shiroFilterFactoryBean.setUnauthorizedUrl("/403.html");
            // 拦截器.
            Map<String, String> filterChainDefinitionMap = new LinkedHashMap<String, String>();
            // 配置不会被拦截的链接 顺序判断
            //静态文件放行
            filterChainDefinitionMap.put("/static/**", "anon");
            //其他
            filterChainDefinitionMap.put("/user/login", "anon");
            // 配置退出过滤器,其中的具体的退出代码Shiro已经替我们实现了
            filterChainDefinitionMap.put("/logout", "logout");
            filterChainDefinitionMap.put("/admin.html", "perms[admin]");
    
            // <!-- 过滤链定义，从上向下顺序执行，一般将 /**放在最为下边 -->:这是一个坑呢，一不小心代码就不好使了;
            // <!-- authc:所有url都必须认证通过才可以访问; anon:所有url都都可以匿名访问-->
            filterChainDefinitionMap.put("/**", "authc");
    
            shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);
            return shiroFilterFactoryBean;
        }
    
        /**
         * 密码校验规则HashedCredentialsMatcher
         * 这个类是为了对密码进行编码的 ,
         * 防止密码在数据库里明码保存 , 当然在登陆认证的时候 ,
         * 这个类也负责对form里输入的密码进行编码
         * 处理认证匹配处理器：如果自定义需要实现继承HashedCredentialsMatcher
         */
        @Bean("hashedCredentialsMatcher")
        public HashedCredentialsMatcher hashedCredentialsMatcher() {
            HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
            //指定加密方式为MD5
            credentialsMatcher.setHashAlgorithmName("MD5");
            //加密次数
            credentialsMatcher.setHashIterations(1);
            credentialsMatcher.setStoredCredentialsHexEncoded(true);
            return credentialsMatcher;
        }
    
    
        @Bean("authRealm")
        @DependsOn("lifecycleBeanPostProcessor")//可选
        public ShiroRealm authRealm(@Qualifier("hashedCredentialsMatcher") HashedCredentialsMatcher matcher) {
            ShiroRealm authRealm = new ShiroRealm();
            authRealm.setAuthorizationCachingEnabled(false);
            authRealm.setCredentialsMatcher(matcher);
            return authRealm;
        }
    
    
        /**
         * 定义安全管理器securityManager,注入自定义的realm
         * @param authRealm
         * @return
         */
        @Bean("securityManager")
        public SecurityManager securityManager(@Qualifier("authRealm") ShiroRealm authRealm) {
            DefaultWebSecurityManager manager = new DefaultWebSecurityManager();
            manager.setRealm(authRealm);
            return manager;
        }
    
    
        /**
         * Spring的一个bean , 由Advisor决定对哪些类的方法进行AOP代理 .
         * @return
         */
        @Bean
        public DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator() {
            DefaultAdvisorAutoProxyCreator creator = new DefaultAdvisorAutoProxyCreator();
            creator.setProxyTargetClass(true);
            return creator;
        }
    
        /**
         * 配置shiro跟spring的关联
         * @param securityManager
         * @return
         */
        @Bean
        public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(@Qualifier("securityManager") SecurityManager securityManager) {
            AuthorizationAttributeSourceAdvisor advisor = new AuthorizationAttributeSourceAdvisor();
            advisor.setSecurityManager(securityManager);
            return advisor;
        }
    
        /**
         * lifecycleBeanPostProcessor是负责生命周期的 , 初始化和销毁的类
         * (可选)
         */
        @Bean("lifecycleBeanPostProcessor")
        public LifecycleBeanPostProcessor lifecycleBeanPostProcessor() {
            return new LifecycleBeanPostProcessor();
        }
    }
```


