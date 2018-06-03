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

