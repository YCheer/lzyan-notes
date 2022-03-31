## 什么是跨域和CORS

- 跨域

URL（Uniform Resource Locator,统一资源定位器）的一般格式如下：
```
协议 + 域名（子域名 + 主域名） + 端口号 + 资源地址
```
如：
```
http://notes.lzyan.fun:8888/hello 由
http + notes + lzyan.fun + 8888 +/hello 组成
```
其中只要协议，子域名，主域名，端口号这四项组成部分中有一项不同，就可以认为是不同的域，也就是不同源，不同的域之间互相访问资源，就被称之为跨域。

浏览器采用同源策略，禁止页面加载或执行与自身来源不同的域的任何脚本。

- CORS

为了解决浏览器同源问题，W3C 提出了跨源资源共享，即 `CORS`(Cross-Origin Resource Sharing)，`CORS`是一个W3C标准。

`CORS` 做到了两点：不破坏即有规则、服务器实现了 CORS 接口，就可以跨源通信。

基于这两点，CORS 将请求分为两类：简单请求和非简单请求。

1. 简单请求：

    在CORS出现前，发送HTTP请求时在头信息中不能包含任何自定义字段，且 HTTP 头信息不超过以下几个字段：

    `Accept`

    `Accept-Language`

    `Content-Language`

    `Last-Event-ID`

    `Content-Type` 只限于 `[application/x-www-form-urlencoded 、multipart/form-data、text/plain]` 类型

    对于简单请求，`CORS` 的策略是请求时在请求头中增加一个 `Origin` 字段，服务器收到请求后，根据该字段判断是否允许该请求访问。

    也就是每一个页面需要返回一个名为 `Access-Control-Allow-Origin` 的 http 请求头来允许外域的站点访问，你可以仅仅暴露有限的资源和有限的外域站点访问

    可以理解为：如果一个请求需要允许跨域访问，则需要在http头中设置 `Access-Control-Allow-Origin` 来决定需要允许哪些站点来访问。即，假如允许`http://notes.lzyan.fun`这个站点的请求跨域，则跨域设置：`Access-Control-Allow-Origin:http://notes.lzyan.fun`。

    除了 `Access-Control-Allow-Origin` ，还有几个字段用于描述 `CORS `的返回结果：

    `Access-Control-Allow-Credentials`：可选，用户是否可以发送、处理`cookie`
    `Access-Control-Expose-Headers`：可选，可以让用户拿到的字段。

2. 非简单请求:

    对于非简单请求的跨源请求，浏览器会在真实请求发出前，增加一次`OPTION`请求，称为预检请求(preflight request)。预检请求将真实请求的信息，包括请求方法、自定义头字段、源信息添加到 HTTP 头信息字段中，询问服务器是否允许这样的操作。

    例如一个`DELETE`请求：
    ```
    OPTIONS /test HTTP/1.1
    Origin: http://www.examples.com
    Access-Control-Request-Method: DELETE
    Access-Control-Request-Headers: X-Custom-Header
    Host: www.examples.com
    ```
    其中与`CORS`相关的字段：
    `Access-Control-Request-Method请求使用的 HTTP 方法`；
    `Access-Control-Request-Headers请求中包含的自定义头字段`

    服务器收到请求时，需要分别对 `Origin`、`Access-Control-Request-Method`、`Access-Control-Request-Headers`进行验证，验证通过后，会在返回`HTTP`头信息中添加 ：
    ```
    Access-Control-Allow-Origin: http://www.examples.com    // 允许CORS的域名
    Access-Control-Allow-Methods: GET, POST, PUT, DELETE  // 允许的方法
    Access-Control-Allow-Headers: X-Custom-Header // 服务器允许使用的字段
    Access-Control-Allow-Credentials: true  // 是否允许用户发送应该包含与请求相关的任何cookie。
    Access-Control-Max-Age: 1728000 // 预检请求的有效期，单位为秒。有效期内，不会重复发送预检请求，缓存
    ```
    

## 跨域请求演示

- 同源请求

编写一个普通的接口controller
```java
@RequestMapping("/normal")
@RestController
public class NormalController {

    @GetMapping("/hello")
    public String hello() {
        return "hello lzyan";
    }
}
```
然后启动浏览器访问：`http://localhost:8080/normal/hello`

![](https://resource.lzyan.fun/PigGo/20211215132045.png)

- 非同源请求

打开任意网站如：`http://notes.lzyan.fun`，然后进入开发者工具（Chrome按F12）,在`console`中输入js代码测试：
```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://127.0.0.1:8080/normal/hello');
xhr.send(null);
xhr.onload = function(e) {
    var xhr = e.target;
    console.log(xhr.responseText);
}
```
回车执行，然后得到结果如下：
```javascript
Access to XMLHttpRequest at 'http://127.0.0.1:8080/normal/hello' from origin 'http://notes.lzyan.fun' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```
结果表明了该请求被 CORS 阻止了，所以是请求失败的。

## 跨域请求的解决方案

### 方案一：使用 @CrossOrigin 注解

Controller类添加`@CrossOrigin`注解
```java
@RequestMapping("/cors1")
@RestController
@CrossOrigin("http://notes.lzyan.fun")// 指定该域名可有访问该类下所有接口，不指定的话就是默认所有域名
public class CorsTestOneController {
    @GetMapping("hello")
    public String hello() {
        return "hello cors1";
    }
}
```
继续像上面跨域请求演示的方法进行测试，输入js代码
```java
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://127.0.0.1:8080/cors1/hello');
xhr.send(null);
xhr.onload = function(e) {
    var xhr = e.target;
    console.log(xhr.responseText);
}
```
回车执行

![](https://resource.lzyan.fun/PigGo/20211215134040.png)

说明这个请求跨域请求成功了。

为了证明是否真的是在指定的域名下允许跨域，此时换一个别的网站:`http://www.lzyan.fun`

![](https://resource.lzyan.fun/PigGo/20211215134301.png)

事实证明该方案成功指定了域名可跨域请求

### 方案二：CORS全局配置——实现WebMvcConfigurer接口

新建一个CorsConfig类，实现WebMvcCOnfigurer接口
```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {
    @Bean
    public WebMvcConfigurer corsConfigurer()
    {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**").
                        allowedOrigins("http://notes.lzyan.fun"). //允许跨域的域名，可以用*表示允许任何域名使用
                        allowedMethods("*"). //允许任何方法（post、get等）
                        allowedHeaders("*"). //允许任何请求头
                        allowCredentials(true). //带上cookie信息
                        exposedHeaders(HttpHeaders.SET_COOKIE).maxAge(3600L); //maxAge(3600)表明在3600秒内，不需要再发送预检验请求，可以缓存该结果
            }
        };
    }
}
```
使用上面编写的普通接口
```java
@RequestMapping("/normal")
@RestController
public class NormalController {

    @GetMapping("/hello")
    public String hello() {
        return "hello lzyan";
    }
}
```
打开`http://notes.lzyan.fun`输入js代码进行测试
```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://127.0.0.1:8080/normal/hello');
xhr.send(null);
xhr.onload = function(e) {
    var xhr = e.target;
    console.log(xhr.responseText);
}
```
回车执行

![](https://resource.lzyan.fun/PigGo/20211215135547.png)

然后像第一种方案一样，去一个非指定的域名下再进行测试

![](https://resource.lzyan.fun/PigGo/20211215135710.png)

所有测试通过，说明此方案的配置成功

### 方案三：过滤器添加请求头

通过过滤器`Filter`，在请求中添加一些请求头`Header`来解决

建一个`CorsFilter`类，实现`javax.servlet`包下`Filter`接口
```java
@Component
public class CorsFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        HttpServletResponse res = (HttpServletResponse) response;
        res.addHeader("Access-Control-Allow-Credentials", "true"); // 可选，用户是否可以发送、处理 cookie
        res.addHeader("Access-Control-Allow-Origin", "http://notes.lzyan.fun"); // 指定域名
        res.addHeader("Access-Control-Allow-Methods", "GET, POST, DELETE, PUT");// 指定方法
        res.addHeader("Access-Control-Allow-Headers", "Content-Type,X-CAF-Authorization-Token,sessionToken,X-TOKEN");
        if (((HttpServletRequest) request).getMethod().equals("OPTIONS")) {
            response.getWriter().println("ok");
            return;
        }
        chain.doFilter(request, response);
    }
}
```
接着用上面的方法进行测试，此时要先把方案二的配置类先注销掉。输入js代码
```java
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://127.0.0.1:8080/normal/hello');
xhr.send(null);
xhr.onload = function(e) {
    var xhr = e.target;
    console.log(xhr.responseText);
}
```
![](https://resource.lzyan.fun/PigGo/20211215140743.png)

![](https://resource.lzyan.fun/PigGo/20211215140759.png)

结果证明，此方法也成功实现了。

### 方案四：返回新的过滤器

新建`CorsConfig`类
```java
@Configuration
public class CorsConfigFilter {
    private CorsConfiguration buildConfig() {
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        corsConfiguration.addAllowedOrigin("*");
        corsConfiguration.addAllowedHeader("*");
        corsConfiguration.addAllowedMethod("*");
        corsConfiguration.setAllowCredentials(false); // When allowCredentials is true, allowedOrigins cannot contain the special value "*"
        return corsConfiguration;
    }

    @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", buildConfig());
        return new CorsFilter(source);
    }
}
```
或者，使用Spring Security时
```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            // by default uses a Bean by the name of corsConfigurationSource
            .cors().and()
            ...
    }

    /**
     * Cors配置优化
     **/
    @Bean
    public CorsConfigurationSource corsConfigurationSource() {
    org.springframework.web.cors.CorsConfiguration configuration = new CorsConfiguration();
    configuration.setAllowedOrigins(singletonList("*"));
    // configuration.setAllowedOriginPatterns(singletonList("*"));
    configuration.setAllowedHeaders(singletonList("*"));
    configuration.setAllowedMethods(Arrays.asList("GET", "POST", "DELETE", "PUT", "OPTIONS");
    configuration.setExposedHeaders(singletonList(SecurityConstants.TOKEN_HEADER));
    configuration.setAllowCredentials(false);
    configuration.setMaxAge(3600L);
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", configuration);
    return source;
    }
}
```


## 参考

- [SpringBoot：处理跨域请求](https://www.cnblogs.com/VanFan/p/11408422.html)