#### 第一步

登录成功，生成Cookie

根据用户生成UUID，服务端得到`key:UUID`和`value:用户信息`，这些信息写入Redis缓存中

登录请求Response的Cookie中写入：`token:生成的UUID的内容`

#### 第二步

跳转页面的Controller中方法直接使用`@CookieValue(value="token")`注解，接收请求携带的UUID

注意，移动端请求可能不会携带Cookie，所以接口需要兼容通过`Header`携带UUID的情况

通过UUID在Redis中查询用户信息

```java
@requestMapping("/to_list")
public String toLogin(Model model, 
                      @CookieValue(value = COOKIE_NAME_TOKEN, required=false) String cookieToken,
                     @RequestParam(value = COOKIE_NAME_TOKEN, required=false) String paramToken) {
  if (StringUtils.isEmpty(cookieToken) && StringUtils.isEmpty(paramToken)) {
    return "login";
  }
  String token = StringUtils.isEmpty(paramToken)?cookieTOken:paramToken;
  User user = userService.getByToken(token);
  model.addAttribute("user", user);
  return "goods_list";
}
```

#### 第三步

刷新Session的有效时间（最后一次访问时，延长Session过期时间）

userService.getByToken在查找到用户信息后，重新调用生成Cookie的方法，即重写Redis值，生成一个新的Cookie

#### 第四部

优化获取用户信息

诸多借口均需要用户信息，每个接口都从`Header`或`Cookie`中获取吗？

- 将User对象作为参数注入到方法里

  ```java
  @Service
  public class UserArgumentResolver implements HandlerMethodArgumentResolver {
      @Autowired
      UserService userService;
      
      public boolean supportsParameter(MethodParameter parameter) {
          Class<?> clazz = parameter.getParameterType();
          return clazz==User.class;
      }
      public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
              NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
          HttpServletRequest request = webRequest.getNativeRequest(HttpServletRequest.class);
          HttpServletResponse response = webRequest.getNativeResponse(HttpServletResponse.class);
          
          String paramToken = request.getParameter(UserService.COOKI_NAME_TOKEN);
          String cookieToken = getCookieValue(request, UserService.COOKI_NAME_TOKEN);
          if(StringUtils.isEmpty(cookieToken) && StringUtils.isEmpty(paramToken)) {
              return null;
          }
          String token = StringUtils.isEmpty(paramToken)?cookieToken:paramToken;
          return userService.getByToken(response, token);
      }
      private String getCookieValue(HttpServletRequest request, String cookiName) {
          Cookie[]  cookies = request.getCookies();
          for(Cookie cookie : cookies) {
              if(cookie.getName().equals(cookiName)) {
                  return cookie.getValue();
              }
          }
          return null;
      }
  }
  ```

- 注册UserArgumentResolver到SpringMVCWebConfig中

  ```java
  @Configuration
  public class WebConfig  extends WebMvcConfigurerAdapter{
      
      @Autowired
      UserArgumentResolver userArgumentResolver;
      
      @Override
      public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
          argumentResolvers.add(userArgumentResolver);
      }   
  }
  ```

  