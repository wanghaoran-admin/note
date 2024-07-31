# Spring Security详解

### 1.简介

 Spring Security 是 Spring家族中的一个安全管理框架。相比与另外一个安全框架Shiro，它提供了更丰富的功能，社区资源也比Shiro丰富。


一般Web应用的需要进行认证和授权。

- `认证`：验证当前访问系统的是不是本系统的用户，并且要确认具体是哪个用户
-  `授权`：经过认证后判断当前用户是否有权限进行某个操作



### 2.核心类

#### UserDetails

`UserDetails` 接口提供了用户的核心信息，Spring Security 使用这个接口进行身份验证和授权。





#### UserDetailsService

`UserDetailsService` 接口用于加载用户特定数据。它定义了一个方法 `loadUserByUsername(String username)`，用于根据用户名获取用户信息。





#### Authentication

`Authentication` 接口表示用户的认证信息，包括凭据（如用户名和密码）、权限

```
AdminDetails loginUser = (AdminDetails) authentication.getPrincipal();
```





#### UsernamePasswordAuthenticationToken 

`UsernamePasswordAuthenticationToken` 是 `Authentication` 接口的一个实现，表示基于用户名和密码的认证。





### 3.认证

#### 依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.5.0</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

  <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
  </dependency>
```

#### 编写用户核心信息

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@JsonIgnoreProperties(ignoreUnknown = true)
public class AdminDetails implements UserDetails {
    private Admin user;

    @JsonIgnore
    private List<SimpleGrantedAuthority> authorities; //变量名称必须叫authorities
    private List<String> permissions = new ArrayList<>(); //当前用户的权限， 存储从数据库中查询到得数据
    public AdminDetails(Admin user) {
        this.user = user;
    }

    //获取权限
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        if (authorities != null) {
            return authorities;
        }

        authorities = new ArrayList<>();
        permissions.add("admin");

        System.out.println("permissions="+permissions);
        for (String permission : permissions) {
            SimpleGrantedAuthority simpleGrantedAuthority = new SimpleGrantedAuthority(permission);
            authorities.add(simpleGrantedAuthority);
        }

        return authorities;
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUserName();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}
```









#### 重写UserDetailsService

```java
@Service
public class UserDetaiServiceImpl implements UserDetailsService {

    @Autowired
    private AdminMapper userMapper;

    /**
     *
     * @param username 用户名
     * @return 返回用户的核心信息
     * @throws UsernameNotFoundException 没有找到用户异常
     */
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        System.out.println("UserDetailsServiceImpl中的去数据库验证身份:" + username);
        QueryWrapper<Admin> qw = new QueryWrapper<>();
        qw.lambda().eq(Admin::getUserName, username);
        Admin user = userMapper.selectOne(qw);
        if (user == null) {
            throw new UsernameNotFoundException("用户不存在");
        }
        System.out.println("这是验证身份的用户：" + user);
        return new AdminDetails(user);
    }
}

```





















### 4.授权























