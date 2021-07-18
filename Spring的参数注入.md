# 			         Spring的参数注入到控制器





## 背景

​		我们经常会有一些上下文的信息需要去系统中获取, 可能多半都是从一些context之类里面去拿(这些数据其实都是在threadlocal中维护), 怎么能让这些参数自动注入到我们的控制器呢



**控制器的代码:**

```
	控制器
	
	@GetMapping("cookie")
    public void testCookie(HttpServletResponse response, HttpServletRequest request, UserInfo userInfo){
        // 业务代码
    }
    
```



很明显这里的这个入参并不是我们的入参,假定这里是用户的基础信息

我们怎么能在请求到来的时候将这个数据注入到控制器呢

按照spring的一贯玩法,首先我们去找一下请求参数处理的相关接口, 如下所示:

* **Strategy interface for resolving method parameters into argument values in**

  **the context of a given request.**

```
/**
 * Strategy interface for resolving method parameters into argument values in
 * the context of a given request.
 *
 * @author Arjen Poutsma
 * @since 3.1
 * @see HandlerMethodReturnValueHandler
 */
public interface HandlerMethodArgumentResolver {

	/**
	 * Whether the given {@linkplain MethodParameter method parameter} is
	 * supported by this resolver.
	 * @param parameter the method parameter to check
	 * @return {@code true} if this resolver supports the supplied parameter;
	 * {@code false} otherwise
	 */
	boolean supportsParameter(MethodParameter parameter);

	/**
	 * Resolves a method parameter into an argument value from a given request.
	 * A {@link ModelAndViewContainer} provides access to the model for the
	 * request. A {@link WebDataBinderFactory} provides a way to create
	 * a {@link WebDataBinder} instance when needed for data binding and
	 * type conversion purposes.
	 * @param parameter the method parameter to resolve. This parameter must
	 * have previously been passed to {@link #supportsParameter} which must
	 * have returned {@code true}.
	 * @param mavContainer the ModelAndViewContainer for the current request
	 * @param webRequest the current request
	 * @param binderFactory a factory for creating {@link WebDataBinder} instances
	 * @return the resolved argument value, or {@code null} if not resolvable
	 * @throws Exception in case of errors with the preparation of argument values
	 */
	@Nullable
	Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer,
			NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception;

}
```



所以我们实现这个接口:

```
public class UserArgumentResolver implements HandlerMethodArgumentResolver {

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
    	// 判断注入的类型  为true的时候选择注入
        Class<?> clazz = parameter.getParameterType();
        return clazz == UserInfo.class;
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
    
    	// 在这里我们就可以按需将我们的相关数据注入到参数中
    
        return new UserInfo("ray","jan");
    }
}
```



然后将这个参数处理器注入到我们的系统中



```
@Configuration
public class WebInjectConfig  extends WebMvcConfigurerAdapter {

    @Autowired
    UserArgumentResolver userArgumentResolver;

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
        //注入自定义参数解析器
        argumentResolvers.add(userArgumentResolver);
    }
}
```

