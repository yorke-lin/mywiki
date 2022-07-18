# 引入SpringSecurity
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

# 默认行为

当Spring项目中引入了Spring Security依赖的时候，项目会默认开启如下配置：
```yaml
security:
  basic:
    enabled: true
```
这个配置开启了一个HTTP basic类型的认证，所有服务的访问都必须先过这个认证
Spring Security的默认配置类`SecurityProperties`中配置了用户名为**user**密码调用`UUID.randomUUID()`生成
```java
// ... User是SecurityProperties的内部类
public static class User {
    private String name = "user";
    private String password = UUID.randomUUID().toString();
    // ...
}
// ...
```
如果需要配置默认的用户名和密码可以
```yaml
spring:
    security:
        user:
            name: xxx
            password: xxx
```
# 自定义认证
## 实现UserDetailsService接口
> 接口中只有loadUserByUsername方法，用于查询用户，返回UserDetail对象
```java
public interface UserDetailsService {
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```
UserDetail包含一些用于描述用户信息的方法，源码如下：
```java
public interface UserDetails extends Serializable {

    Collection<? extends GrantedAuthority> getAuthorities();

    String getPassword();

    String getUsername();

    boolean isAccountNonExpired();

    boolean isAccountNonLocked();

    boolean isCredentialsNonExpired();

    boolean isEnabled();
}
```
- `getAuthorities()`获取用户包含的权限，返回权限集合，权限是一个继承了GrantedAuthority的对象；
- `getPassword()`和`getUsername()`用于获取密码和用户名；
- `isAccountNonExpired()`方法返回boolean类型，用于判断账户是否未过期，未过期返回true反之返回
- `isAccountNonLocked()`方法用于判断账户是否未锁定；
- `isCredentialsNonExpired()`用于判断用户凭证是否没过期，即密码是否未过期；
- `isEnabled()`方法用于判断用户是否可用；

## 实现PasswordEncoder接口
> 用于加密/验证用户密码
PasswordEncoder源码
```java
public interface PasswordEncoder {
    String encode(CharSequence var1);

    boolean matches(CharSequence var1, String var2);

    default boolean upgradeEncoding(String encodedPassword) {
        return false;
    }
}
```
- `encode`方法用来对明文密码进行加密，返回加密之后的密文。
- `matches`方法是一个密码校对方法，在用户登录的时候，将用户传来的明文密码和数据库中保存的密文密码作为参数，
传入到这个方法中去，根据返回的 Boolean 值判断用户密码是否输入正确。
- `upgradeEncoding`


## 实现Spring Security 配置类
> 主要功能：配置哪些URL不需要认证，哪些需要认证
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    /**
     * 自定义用户认证逻辑
     */
    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            // CSRF禁用，因为不使用session
            .
    }
    
    /**
     * 密码加密实现
     */
    @Bean
    public BCryptPasswordEncoder encoder() {
        // 实例化对应的加密实现类
        return new BCryptPasswordEncoder();
    }
    
    /**
     * 
     */
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/js/**", "/css/**","/images/**");
    }
    
    /**
     * 身份认证接口
     */
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 覆盖默认获取用户认证实现
        auth.userDetailsService(userDetailsService)
            // 调用加密实现
            .passwordEncoder(encoder());
    }
}
```

