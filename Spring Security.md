# Spring Security

## Servlet Applications

### Servlet Security

#### DelegatingFilterProxy

允许在Servlet容器的生命周期和Spring的ApplicationContext之间架起桥梁。Servlet容器允许使用自己的标准注册过滤器，但是它不知道Spring定义的bean。DelegatingFilterProxy可以通过标准的Servlet容器机制注册，但是将所有工作委托给实现Filter的springbean。

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200702113852249.png" alt="image-20200702113852249"/>

#### FilterChainProxy

为每个request确定哪个SecurityFilterChain将会被调用,可以应用在不同的场景（例如: 密码登录，短信登录）

![image-20200702114125553](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200702114125553.png)

#### SecurityFilterChain

![image-20200702114422131](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200702114422131.png)

例如：/api/**走第一个过滤链，/**走第二个过滤链

>  注意：如果/api/在首先匹配上了，则不会进入第二个过滤链

#### Security Filters

具体的过滤链

- ChannelProcessingFilter
- ConcurrentSessionFilter
- WebAsyncManagerIntegrationFilter
- SecurityContextPersistenceFilter
- HeaderWriterFilter
- CorsFilter
- CsrfFilter
- LogoutFilter
- OAuth2AuthorizationRequestRedirectFilter
- Saml2WebSsoAuthenticationRequestFilter
- X509AuthenticationFilter
- AbstractPreAuthenticatedProcessingFilter
- CasAuthenticationFilter
- OAuth2LoginAuthenticationFilter
- Saml2WebSsoAuthenticationFilter
- [`UsernamePasswordAuthenticationFilter`](https://docs.spring.io/spring-security/site/docs/5.3.2.RELEASE/reference/html5/#servlet-authentication-usernamepasswordauthenticationfilter)
- ConcurrentSessionFilter
- OpenIDAuthenticationFilter
- DefaultLoginPageGeneratingFilter
- DefaultLogoutPageGeneratingFilter
- [`DigestAuthenticationFilter`](https://docs.spring.io/spring-security/site/docs/5.3.2.RELEASE/reference/html5/#servlet-authentication-digest)
- BearerTokenAuthenticationFilter
- [`BasicAuthenticationFilter`](https://docs.spring.io/spring-security/site/docs/5.3.2.RELEASE/reference/html5/#servlet-authentication-basic)
- RequestCacheAwareFilter
- SecurityContextHolderAwareRequestFilter
- JaasApiIntegrationFilter
- RememberMeAuthenticationFilter
- AnonymousAuthenticationFilter
- OAuth2AuthorizationCodeGrantFilter
- SessionManagementFilter
- [`ExceptionTranslationFilter`](https://docs.spring.io/spring-security/site/docs/5.3.2.RELEASE/reference/html5/#servlet-exceptiontranslationfilter)
- [`FilterSecurityInterceptor`](https://docs.spring.io/spring-security/site/docs/5.3.2.RELEASE/reference/html5/#servlet-authorization-filtersecurityinterceptor)
- SwitchUserFilter

#### Handling Security Exceptions

ExceptionTranslationFilter 会转化AccessDeniedException和AuthenticationException为HTTP响应。

![image-20200702143149823](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200702143149823.png)

1. ExceptionTranslationFilter 会调用FilterChain.doFilter(request,response)去处理剩余的程序
2. 如果用户没有认证或者它是一个AuthenticationException，则Start Authentication。
   - SecurityContextHolder被清除
   - HttpServletRequest 被保存进RequestCache.当用户成功认证，RequestCache将被作为原始请求
   - AuthenticationEntryPoint用于客户端的请求凭证。例如，他可能重定向登录页，或者发送www-Authenticate头
3. AccessDeniedException，将会被AccessDeniedHandler去处理。

>注意： 如果程序没有抛出AuthenticationException 或者AccessDeniedException，则ExceptionTranslationFilter 将不会做任何事

ExceptionTranslationFilter 伪代码：

```java
try {
    filterChain.doFilter(request,response);
} catch (AccessDeniedException | AuthenticationException  e){
    if (!authenticated || e instanceof AuthenticationException) {
        startAuthentication();
    } else {
        accessDenied();
    }
}
```

### Authentication

> 架构组件

- SecurityContextHolder：SecurityContextHolder存储了谁认证的细节
- SecurityContext：从SecurityContextHolder获得和包含当前用户认证的Authentication。
- Authentication：可以被放进AuthenticationManager来提供一个用户做了认证或者用户来自SecurityContext的凭证
- GrantedAuthority：一个被授予给主体（principal ）Anthentication的授权（例如：roles，scopes等）
-  AuthenticationManager：定义过滤链执行认证的API
- ProviderManager：AuthenticationManager最常见实现
- AuthenticationProvider：被ProviderManager用于执行指定类型的认证
- Request Credentials with AuthenticationEntryPoint：用于来自客户端的请求凭证（例如：重定向登录页，发送www-Authentication响应等）
- AbstractAuthenticationProcessingFilter：用于认证的基本Filter。

> 认证机制

- Username and Password：用username/password认证
- Oauth 2.0 Login：OAuth 2.0使用OpenID登录连接和非标准OAuth 2.0登录(例如GitHub)
- SAML 2.0 Login：SAML 2.0 登录
- Central Authentication Server（CAS）：中心认证服务支持
- Remember Me：如何记住用户过去的会话过期
- JAAS Authentication：JAAS认证
- OpenID：OpenID认证（不与OpenID Connect混淆）
- Pre-Authencation Scenarios：使用外部机制（如SiteMinder或javaee-security）进行身份验证，但仍使用Spring安全性进行授权和防止常见漏洞攻击。
- X509 Authentication：X509 认证