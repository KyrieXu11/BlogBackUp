---
title: springboot整合jwt
date: 2020-02-13 12:59:18
tags: springboot,jwt
categories: springboot
---

# 确定jwt所用的库版本

我这里使用的jwt版本是`jjwt`，网址：https://github.com/jwtk/jjwt

# 添加jwt依赖

```xml
<dependencies>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-api</artifactId>
        <version>0.11.0</version>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-impl</artifactId>
        <version>0.11.0</version>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-jackson</artifactId> <!-- or jjwt-gson if Gson is preferred -->
        <version>0.11.0</version>
        <scope>runtime</scope>
    </dependency>
```

项目不止这些依赖，还需要`springboot`和`springsecurity`的依赖，这里就不添加了。

# JWT的客户端和服务端的交互原理

下面都是个人理解所画的图，可能有错

![jwt_sequence.png](https://i.loli.net/2020/02/13/PLHUyjfp5ia6Z4V.png)

# JWT过滤器原理

![jwt_filter.png](https://i.loli.net/2020/02/13/gbZmzwqi8QtuANc.png)

一个请求需要通过多个过滤器才能接触到接口，而过滤器是需要手动去写的

## 添加第一个过滤器——`UsernamePasswordAuthenticationFilter`

![jwt_usernamepasswordauthenticationfilter.png](https://i.loli.net/2020/02/13/FfPGxhMKcu4nN21.png)

```java
/**
 * @author KyrieXu
 * @date 2020/2/12 15:34
 **/
@EqualsAndHashCode(callSuper = true)
@Data
@AllArgsConstructor
@NoArgsConstructor
public class JwtAuthFilter extends UsernamePasswordAuthenticationFilter {

    private AuthenticationManager authenticationManager;

    public static final String key="securesecuresecuresecuresecuresecuresecuresecuresecuresecuresecuresecuresecuresecuresecuresecure";


//    下面的方法成功认证之后会到这个方法来，这个方法产生jwt的token
    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authResult) throws IOException, ServletException {
//        subject和jwt的字段是有关系的，忘了的话可以取官网看下debugger
//        第一个是设置headers的token
//        第二个是设置body的token
//        key转换成byte数组长度必须大于256
        String token = Jwts.builder()
                .setSubject(authResult.getName())
                .claim("authorities", authResult.getAuthorities())
                .setIssuedAt(new Date())
//                设置过期时间
                .setExpiration(java.sql.Date.valueOf(LocalDate.now().plusWeeks(2)))
//                设置签名signature,这个是至关重要的
                .signWith(Keys.hmacShaKeyFor(key.getBytes()))
                .compact();
//        token放在header里面，最好要在token前面加上认证方式
        response.addHeader("Authorization","Bearer "+token);

    }

    // 这个注解是Lombok的注解，抛出异常，简化代码
    @SneakyThrows
    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {
//        从request的inputstream读取内容(json格式)放到UPAuthRequest类中，就是快捷赋值创建对象
        UPAuthRequest authenticationRequest = new ObjectMapper().readValue(request.getInputStream(), UPAuthRequest.class);
        Authentication authentication=new UsernamePasswordAuthenticationToken(
                authenticationRequest.getUsername(),
                authenticationRequest.getPassword()
        );
//        确定用户名是否存在以及密码是否正确
        return authenticationManager.authenticate(authentication);
    }
}
```

上面的代码说两句吧：

1. 首先执行`attemptAuthentication`这个方法，如果校验成功，那么就继续执行`successfulAuthentication`这个方法来生成jwt，如果校验失败则抛出异常。
2. 在将token返回给客户端的时候，需要在token之前加上token的类型，我这里其实加和不加都一样的，但是在实际操作中，加上类型可以灵活的判断token的类型，进行不一样的业务处理。
3. key是一个字符串，在其转换成byte数组的时候，数组长度必须大于256。
4. 还有里面有一个`UPAuthRequest`，其实就是用户名和密码的一个自定义的类。

## 添加第二个拦截器——`OncePerRequestFilter`

![jwt_OncePerRequestFilter.png](https://i.loli.net/2020/02/13/tKR7MvfL2Bp6zEP.png)

```java
/**
 * @author KyrieXu
 * @date 2020/2/12 17:58
 **/
public class JwtTokenVerifier extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        String tokenHeader = request.getHeader("Authorization");
        if (!StringUtils.hasText(tokenHeader) || !tokenHeader.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }
//        从header提取token
        String token = tokenHeader.replace("Bearer ", "");
        try {
//        解析token
            Jws<Claims> claimsJws = Jwts.parser()
                    .setSigningKey(Keys.hmacShaKeyFor(JwtAuthFilter.key.getBytes()))
                    .parseClaimsJws(token);
//        获取body
            Claims body = claimsJws.getBody();
//        获取用户名，对照着 https://jwt.io/ 的debugger来看
            String username = body.getSubject();
            List<Map<String, String>> authorities = (List<Map<String, String>>) body.get("authorities");
            Set<SimpleGrantedAuthority> simpleGrantedAuthorities = new HashSet<>();
            for (Map<String, String> authoritys : authorities) {
                String authority = authoritys.get("authority");
                simpleGrantedAuthorities.add(new SimpleGrantedAuthority(authority));
            }
//            将权限放入authentication
            Authentication authentication = new UsernamePasswordAuthenticationToken(
                    username,
                    null,
                    simpleGrantedAuthorities
            );
            SecurityContextHolder.getContext().setAuthentication(authentication);
        }catch (JwtException e){
            throw new JwtException(String.format("Token %s 不可信",token));
        }
        filterChain.doFilter(request,response);
    }
}
```

## 配置Spring Security

```java
/**
 * @author KyrieXu
 * @date 2020/2/12 11:36
 **/
@EnableWebSecurity
@Configuration
public class CusSecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder(10);
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        PasswordEncoder passwordEncoder=passwordEncoder();
        auth.inMemoryAuthentication()
                .withUser("kyriexu").roles("admin").password(passwordEncoder.encode("123"))
                .and()
                .withUser("xu").roles("student").password(passwordEncoder.encode("123"));
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/","/login").permitAll()
                .antMatchers("/admin/**").hasRole("admin")
                .antMatchers("/student/**").hasAnyRole("admin","student")
                .anyRequest().authenticated()
                .and()
                .csrf().disable()
                .sessionManagement()
//                因为jwt是无状态的，所以要设置session无状态
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
//                传进去父类的authenticationmanager
                .addFilter(new JwtAuthFilter(authenticationManager()))
//                表示这个过滤器在上面那个过滤器后面
                .addFilterAfter(new JwtTokenVerifier(),JwtAuthFilter.class);
    }
}
```

还是说一下上面的代码：

1. Spring Security的常规配置不用多说了
2. 就是添加2个拦截器，注意顺序就行.
3. AuthenticationManager==不能使用==自动注入，因为Filter不是spring组件，自动注入是为空的，而且如果把Filter表示为组件之后，就会抛出异常，运行不了。

## 编写接口测试

```java
/**
 * @author KyrieXu
 * @date 2020/2/12 11:44
 **/
@RestController
public class HelloController {

    @GetMapping("/")
    public String get() {
        return "get";
    }

    @PostMapping("/")
    public String post() {
        return "post";
    }

    @PutMapping("/")
    public String put() {
        return "put";
    }

    @DeleteMapping("/")
    public String delete() {
        return "delete";
    }

    @GetMapping("/student/123")
    public Authentication StuGet(){
        return SecurityContextHolder.getContext().getAuthentication();
    }

    @GetMapping("/admin/123")
    public Authentication AdminGet(){
        return SecurityContextHolder.getContext().getAuthentication();
    }
}
```

发送登陆请求：

![jwt_login.png](https://i.loli.net/2020/02/13/opAWYPCfcjZE4mB.png)

测试接口，成功！

![jwt_api_test.png](https://i.loli.net/2020/02/13/X3rzRGLQUDFZ8Oa.png)