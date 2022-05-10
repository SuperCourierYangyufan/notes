# springSecurity

1. UserDetailsService
	* 需要实现public User loadUserByUsername(String username)来自定义登录
	* UserDetails子类 User为登录用户实体,一般继承他封装实际信息
	* 注入角色信息,只要前缀 ROLE_ 如 ROLE_user
2. PasswordEncoder
	* 为密码加密和比较的接口
	* 一般采用BCryptPasswordEncoder实现类
	* String encode(CharSequence rawPassword); 加密处理
	* boolean matches(CharSequence rawPassword, String encodedPassword); 比较密码处理
3. UsernamePasswordAuthenticationFilter
	* 为拦截器,规定了请求为POST,输入为username,password.可通过httpSecurity.usernameParameter().passwordParameter()来重新确定输入的key
	* httpSecurity.addFilterBefore(自定义拦截器, UsernamePasswordAuthenticationFilter.class);可做自定义拦截器
4. 处理器 
	* httpSecurity.formLogin().successHandler(null).failureForwardUrl(null); 登录成功失败处理器
	* httpSecurity.logout().logoutSuccessHandler(null); 退出成功处理器
	* httpSecurity.exceptionHandling().accessDeniedHandler() 权限不足处理器
5. httpSecurity.anyRequest().authenticated()
	* 所有请求都要认证
	* 链式表达式为从前往后的权重顺序,所以需要放在放行后面
6. 路径规则
	* .antMatchers(ant表达式,用户匹配规则).permitAll()
		- ?:一个字符
		- *:0个或多个字符
		- **:匹配一个或多个目录
	* .regexMatchers(可选参数:[请求方式,HttpMethod.POST],正则表达式).permitAll()
7. 路径权限
	* .permitAll() 允许所有,无需登录
	* .denyAll() 禁止
	* .anonymous() 必须是没有登录的才能访问
	* .authenticated() 必须要认证
	* .fullyAuthenticated() 必须用户名加密码输入进来的
	* .rememberMe() 记住免登录
	* .hasAuthority(权限) 需要某权限才能访问
	* .hasAnyAuthority(权限,权限,权限,权限) 只要有其中一个权限就可以访问
	* .hasRole(角色) 角色访问
	* .hasAnyRole(角色,角色,角色) 只要有其中一个角色就可以访问
	* .hasIpAddress(IP/port) 指定地址可以访问
	* 自定义
		- .access("@实现类.实现的方法(request,authentication)")
		- 实现类,需要注入spring
			```
			public boolean hasPermission(HttpServletRequest request, Authentication authentication){
				Object obj = authentication.getPrincipal();
				if(obj instanceof UserDetails){
					UserDetails user = (UserDetails) obj;
					//该权限需要存放URI
					Collection<? extends GrantedAuthority> authorities = user.getAuthorities();
					return authorities.contains(new SimpleGrantedAuthority(request.getRequestURI()));
				}
				return false;
			}
			```
	* 注解
		- @EnableGlobalMethodSecurity(securedEnabled = true,prePostEnabled = true,)全局开启
		- @Secured("ROLE_角色") 需要角色访问
		- @PreAuthorize("hasRole()"||"hasAuthority()") 操作前判断权限
		- @PostAuthorize("hasRole()"||"hasAuthority()") 操作后判断权限
8. 记住我
	* httpSecurity.rememberMe().tokenRepository(JdbcTokenRepositoryImpl的Bean).tokenValiditySeconds(超时时间-秒).userDetailsService(自定义登录逻辑bean)
	* JdbcTokenRepositoryImpl
		- jdbcTokenRepository.setDataSource(); 设置数据源
		- jdbcTokenRepository.setCreateTableOnStartup(true); 自动建表,第二次需要关闭
9. csrf
	* 跨域请求伪造
	* 登录成功后会返回_csrf的token,以后每次请求需要带上
10. springSecurity Oauth2端点
	* Authorize Endpoint 授权端点,进行授权
		- /oauth2/Authorize
	* Token Endpoint 令牌端点,经过授权拿到Token
		- /oauth2/token
	* Introspection Endpoint 校验端点,校验Token合法性
		- /oauth2/introspect
	* Revocation Endpoint 撤销端点,撤销授权
		- /oauth2/revoke
11. 授权服务器配置
	* @EnableAuthorizationServer 
	* 需要继承 AuthorizationServerConfigurerAdapter
		- 实现 public void configure(ClientDetailsServiceConfigurer clients)方法 客户端管理
		- 实现 public void configure(AuthorizationServerSecurityConfigurer oauthServer) 安全约束
		- 实现 public void configure(AuthorizationServerEndpointsConfigurer endpoints)  授权配置
		```
		public void configure(AuthorizationServerEndpointsConfigurer endpoints) {

		//配置端点
		endpoints.tokenStore(tokenStore)
			.authenticationManager(authenticationManager)
			.userDetailsService(userDetailsService);

		//扩展token返回结果,jwtTokenEnhancer见13
		if (jwtAccessTokenConverter != null && jwtTokenEnhancer != null) {
			TokenEnhancerChain tokenEnhancerChain = new TokenEnhancerChain();
			List<TokenEnhancer> enhancerList = new ArrayList<>();
			enhancerList.add(jwtTokenEnhancer);
			enhancerList.add(jwtAccessTokenConverter);
			tokenEnhancerChain.setTokenEnhancers(enhancerList);
			endpoints.tokenEnhancer(tokenEnhancerChain).accessTokenConverter(jwtAccessTokenConverter);
		}
	}
		```
12. 资源服务器配置
	* @EnableResourceServer
	* 需要继承 ResourceServerConfigurerAdapter
		- 实现 public void configure(HttpSecurity http),进行资源的管理
13. jwt
	* 三部分构成
		- 头部 主要为类型和加密算法 然后base64加密生产字段1
		- 负载 分为标准信息和自定义信息 然后base64加密生产字段2
		- 签证 字段1+字段2+盐(保密) 然后通过头部声明的加密算法生成字段3
		- 返回的就是 字段1.字段2.字段3
	* 公共荷载
		- iss: jwt签发者
		- sub: jwt所面向的用户
		- aud: 接收jwt的一方
		- exp: jwt的过期时间，这个过期时间必须要大于签发时间
		- nbf: 定义在什么时间之前，该jwt都是不可用的.
		- iat: jwt的签发时间
		- jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。
	* jwt整合springSecurity配置类
		```
		@Configuration
		public class JwtTokenStoreConfiguration {
		
			/**
			* 使用jwtTokenStore存储token
			*/
			@Bean
			public TokenStore jwtTokenStore() {
				return new JwtTokenStore(jwtAccessTokenConverter());
			}
		
			/**
			* 用于生成jwt,本质就是将原本token进行转化jwt
			*/
			@Bean
			public JwtAccessTokenConverter jwtAccessTokenConverter() {
				JwtAccessTokenConverter accessTokenConverter = new JwtAccessTokenConverter();
				accessTokenConverter.setSigningKey(TokenConstant.SIGN_KEY);
				return accessTokenConverter;
			}
		
			/**
			* 用于扩展jwt,荷载里加东西
			*/
			@Bean
			@ConditionalOnMissingBean(name = "jwtTokenEnhancer")
			public TokenEnhancer jwtTokenEnhancer() {
				return new一个TokenEnhancer的实现类
			}
		
		}
		```
14. 刷新令牌
	* 客户端授权开启refresh_token
	* 已经请求/oauth/token,grant_type:refresh_token refresh_token:上次返回中的refresh_token
