# Spring Security详解

### 1.简介

 Spring Security 是 Spring家族中的一个安全管理框架。相比与另外一个安全框架Shiro，它提供了更丰富的功能，社区资源也比Shiro丰富。


一般Web应用的需要进行认证和授权。

- `认证`：验证当前访问系统的是不是本系统的用户，并且要确认具体是哪个用户
-  `授权`：经过认证后判断当前用户是否有权限进行某个操作







### 2.认证与授权

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

#### 用户核心类

```java
package org.example.springsecuritylian.springsecurity;

import com.alibaba.fastjson.annotation.JSONField;
import com.fasterxml.jackson.annotation.JsonIgnore;
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.example.springsecuritylian.entity.User;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.ArrayList;
import java.util.Collection;
import java.util.List;

@Data
@AllArgsConstructor
@NoArgsConstructor
@JsonIgnoreProperties(ignoreUnknown = true)
public class UserDetail implements UserDetails {

    private User user;

    private List<String> permissions; //当前用户的权限，存储从数据库中查询到得数据

    @JSONField(serialize = false)
    private List<SimpleGrantedAuthority> authorities; //变量名称必须叫authorities，这里存储的是用户能够访问权限


    public UserDetail(User user, List<String> permissions) {
        this.user = user;
        this.permissions = permissions;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        if (authorities != null) {
            return authorities;
        }
        //将用户从数据库中查询到得信息， 存储到认证框架中得授权方法中
        authorities = new ArrayList<>();
        for (String permission : permissions) {
            //赋予用户权限
            SimpleGrantedAuthority simpleGrantedAuthority = new SimpleGrantedAuthority(permission);
            authorities.add(simpleGrantedAuthority);
        }
        return authorities;
    }

    @Override
    public String getPassword() {
        return user.getUserPassword();
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









#### 重写验证方法

```java
@Service
public class UserDetaiServiceImpl implements UserDetailsService {
    @Autowired
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(User::getUserName,s);
        User user = userMapper.selectOne(queryWrapper);
        if (user==null) throw new UsernameNotFoundException("用户不存在！！！");
        System.out.println("验证身份的用户:"+user);
        List<String> permissions = new ArrayList<>(); //这里的权限数据要从数据库中查询
        
        /**
        * SELECT * FROM `user` u,`role_user` ru,`role_menu` rm,`role` r,`menu` m
        * WHERE u.`user_name` = "wang"  #用户名称
        * AND u.`user_id` = ru.`user_id` # 用户id和角色用户表关联，查出该用户对应的角色
        * AND ru.`role_id` = r.`id` # 用户角色id和角色关联
        * AND rm.`role_id` =r.`id` # 用户角色id和角色关联
        * AND rm.`menu_id` = m.`menu_id`  #权限和权限表关联
        */
        
        
        permissions.add("admin");
        permissions.add("cscscs");
        return new UserDetail(user,permissions);
    }
}
```





#### 过滤器

```java
@Component
public class PowerFilter extends OncePerRequestFilter {
    @Autowired
    private RedisTemplate redisTemplate;

    //每次访问controller都会进入此方法
    @Override
    protected void doFilterInternal(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, FilterChain filterChain) throws ServletException, IOException {
        System.out.println("进入拦截器.....");
        
        String token = httpServletRequest.getHeader("token");
        
         //验证令牌必须是字符串类型
        if (!StringUtils.hasText(token)) {
            filterChain.doFilter(httpServletRequest, httpServletResponse);
            return;
        }
        
        Map map = new HashMap();
        try {
            map = JwtUtil.parseToken(token); //进行令牌的检验
        } catch (Exception e) {
            e.printStackTrace();
            throw new RuntimeException("令牌失效!"); //如果解析失败就说明令牌错误，或者过期！！
        }

        //能够解析就从redis中取出
        UserDetail loginUser = (UserDetail) redisTemplate.boundValueOps("login:" +map.get("name")).get();
        System.out.println("redis中的数据"+loginUser);
        
        //进行二次检测确保redis中有该对象【没有被删除】
        if (ObjectUtils.isEmpty(loginUser)) {
            throw new RuntimeException("没有数据!");
        }

       	//进行身份验证令牌
        UsernamePasswordAuthenticationToken usernamePasswordAuthenticationToken = new UsernamePasswordAuthenticationToken(loginUser, null, loginUser.getAuthorities());
        
        SecurityContextHolder.getContext().setAuthentication(usernamePasswordAuthenticationToken);
        //放行接口
        filterChain.doFilter(httpServletRequest, httpServletResponse);
    }
}

```









#### 核心配置类

```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true) //开启授权
public class MyWebSecurityConfigurer extends WebSecurityConfigurerAdapter {

    @Bean //配置加密
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Autowired
    private PowerFilter powerFilter;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 允许匿名访问
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();
        //将用户自定义的拦截器添加到指定系统拦截器器之前运行
        http.addFilterBefore(powerFilter, UsernamePasswordAuthenticationFilter.class);
        
        //添加用户自定义得验证和授权得异常处理类
        //accessDeniedHandler 配置用户权限
        //authenticationEntryPoint 配置用户认证
        http.exceptionHandling().accessDeniedHandler(accessDeniedHandler).authenticationEntryPoint(authenticationEntryPoint);    }

    
    //用来处理认证逻辑的核心接口
    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }

}
```





#### 控制器

```java
@RestController
@RequestMapping("/user")
public class UserController {
    @Autowired
    private RedisTemplate redisTemplate;
    @Autowired
    private AuthenticationManager authenticationManager;


    @GetMapping("show")
    @PreAuthorize("hasAuthority('ROLE_admin1')") //这里的注解必须加上
    public String show(){
        return "你好!!";
    }

    @GetMapping("login")
    public Result login(String name,String pwd){
        UsernamePasswordAuthenticationToken usernamePasswordAuthenticationToken = new UsernamePasswordAuthenticationToken(name, pwd);
        Authentication authenticate = null;
        authenticate = authenticationManager.authenticate(usernamePasswordAuthenticationToken);

        UserDetail loginUser = (UserDetail) authenticate.getPrincipal();

        Map map = new HashMap();
        map.put("name",loginUser.getUser().getUserName());
        String token = JwtUtil.genToken(map);

        // 用户存储到 redis
        redisTemplate.boundValueOps("login:" + loginUser.getUser().getUserName()).set(loginUser);

        Map<String, String> map1 = new HashMap<>();
        map1.put("token", token);

        return new Result(200, map1, "认证通过！");
    }
}
```











#### 异常处理

如果不处理异常会返回以下代码，只有一个403很难知道是权限异常还是认证异常

```java
{
    "timestamp": "2024-07-31T13:50:21.232+00:00",
    "status": 403,
    "error": "Forbidden",
    "path": "/user/show1"
}
```



#### WebUtil工具类

```java
public class WebUtils {
    /**
     * 将字符串渲染到客户端
     *
     * @param response 渲染对象
     * @param string   待渲染的字符串
     * @return null
     */
    public static String renderString(HttpServletResponse response, String string) {
        try {
            response.setHeader("Access-Control-Allow-Origin", "*");
            response.setHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE");
            response.setHeader("Access-Control-Max-Age", "3600");
            response.setHeader("Access-Control-Allow-Headers", "*");
            response.setStatus(200);
            response.setContentType("application/json");
            response.setCharacterEncoding("utf-8");
            response.getWriter().print(string);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```





【需要在核心配置文件中进行添加异常处理】

**处理权限不足异常**

```java
@Component
public class AccessDeniedHandlerImpl implements AccessDeniedHandler {

    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException, ServletException {
        Result r = new Result(500,HttpStatus.FORBIDDEN.value(), "用户权限不足!");
        WebUtils.renderString(response, JSON.toJSONString(r));
    }
}
```



**处理认证失败异常**

```java
@Component
public class AuthenticationEntryPointImpl implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
        Result r = new Result(500,HttpStatus.FORBIDDEN.value(), "用户认证失败！");
        WebUtils.renderString(response, JSON.toJSONString(r));
    }
}
```



#### 流程

用户发送请求----->

过滤器---->过滤掉---->没有验证或权限

过滤器---->没过滤掉---->进入控制器执行对应的方法



如果是登录是还会触发

```java
   //身份验证
        Authentication authenticate = null;
        authenticate = authenticationManager.authenticate(usernamePasswordAuthenticationToken);
```

也就是以下的代码

```java
@Service
public class UserDetaiServiceImpl implements UserDetailsService {
    @Autowired
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        System.out.println(3);
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(User::getUserName,s);
        User user = userMapper.selectOne(queryWrapper);
        if (user==null) throw new UsernameNotFoundException("用户不存在！！！");
        System.out.println("验证身份的用户:"+user);
        List<String> permissions = new ArrayList<>();
        permissions.add("admin");
        permissions.add("cscscs");
        return new UserDetail(user,permissions);
    }
}
```

















### 3.用户|角色|权限 

在Spring Security以及许多身份管理系统中，用户、角色和权限是管理系统访问控制和授权的核心概念。



#### 关系

**用户与角色的关系**：

- **多对多**：一个用户可以拥有多个角色，一个角色可以分配给多个用户。
- **示例**：用户`Alice`可能有`Admin`和`Editor`角色。角色`Admin`和`Editor`也可以被其他用户拥有。

**角色与权限的关系**：

- **多对多**：一个角色可以包含多个权限，一个权限可以属于多个角色。
- **示例**：角色`Admin`可能有"READ_PRIVILEGES"、"WRITE_PRIVILEGES"和"DELETE_PRIVILEGES"等权限。而角色`Editor`可能只有"READ_PRIVILEGES"和"WRITE_PRIVILEGES"权限。

**用户与权限的关系**：

- **间接关系**：用户通过角色间接获得权限。用户被分配角色，角色中定义了权限。





#### 表设计

```sql
/*
 Navicat Premium Data Transfer

 Source Server         : localhost_3306_1
 Source Server Type    : MySQL
 Source Server Version : 80028 (8.0.28)
 Source Host           : localhost:3306
 Source Schema         : springsecurity

 Target Server Type    : MySQL
 Target Server Version : 80028 (8.0.28)
 File Encoding         : 65001

 Date: 31/07/2024 21:56:42
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for menu
-- ----------------------------
DROP TABLE IF EXISTS `menu`;
CREATE TABLE `menu`  (
  `menu_id` int NOT NULL AUTO_INCREMENT,
  `menu_name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `menu_path` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`menu_id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 7 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of menu
-- ----------------------------
INSERT INTO `menu` VALUES (1, '查看成绩', '/student/show');
INSERT INTO `menu` VALUES (2, '查看修改', '/student/update');
INSERT INTO `menu` VALUES (3, '查看学生', '/student/getById');

-- ----------------------------
-- Table structure for role
-- ----------------------------
DROP TABLE IF EXISTS `role`;
CREATE TABLE `role`  (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 4 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of role
-- ----------------------------
INSERT INTO `role` VALUES (1, '学生');
INSERT INTO `role` VALUES (2, '老师');
INSERT INTO `role` VALUES (3, '管理员');

-- ----------------------------
-- Table structure for role_menu
-- ----------------------------
DROP TABLE IF EXISTS `role_menu`;
CREATE TABLE `role_menu`  (
  `role_id` int NULL DEFAULT NULL,
  `menu_id` int NULL DEFAULT NULL
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of role_menu
-- ----------------------------
INSERT INTO `role_menu` VALUES (1, 1);
INSERT INTO `role_menu` VALUES (1, 2);
INSERT INTO `role_menu` VALUES (2, 1);
INSERT INTO `role_menu` VALUES (2, 2);
INSERT INTO `role_menu` VALUES (2, 3);

-- ----------------------------
-- Table structure for role_user
-- ----------------------------
DROP TABLE IF EXISTS `role_user`;
CREATE TABLE `role_user`  (
  `role_id` int NULL DEFAULT NULL,
  `user_id` int NULL DEFAULT NULL
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of role_user
-- ----------------------------
INSERT INTO `role_user` VALUES (1, 1);
INSERT INTO `role_user` VALUES (1, 2);
INSERT INTO `role_user` VALUES (2, 3);

-- ----------------------------
-- Table structure for user
-- ----------------------------
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user`  (
  `user_id` int NULL DEFAULT NULL,
  `user_name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `user_password` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of user
-- ----------------------------
INSERT INTO `user` VALUES (1, 'wang', '$2a$10$xAuoQfRUm6AhAKxFHN/GHe7p5IkhV0b.1WwNuHndLuqkATXg/ntKG');
INSERT INTO `user` VALUES (2, 'zhang', '$2a$10$xAuoQfRUm6AhAKxFHN/GHe7p5IkhV0b.1WwNuHndLuqkATXg/ntKG');
INSERT INTO `user` VALUES (3, 'hua', '$2a$10$xAuoQfRUm6AhAKxFHN/GHe7p5IkhV0b.1WwNuHndLuqkATXg/ntKG');

SET FOREIGN_KEY_CHECKS = 1;

```



#### 根据用户查询对应的权限

```sql
SELECT * FROM `user` u,`role_user` ru,`role_menu` rm,`role` r,`menu` m
        * WHERE u.`user_name` = "wang"  #用户名称
        * AND u.`user_id` = ru.`user_id` # 用户id和角色用户表关联，查出该用户对应的角色
        * AND ru.`role_id` = r.`id` # 用户角色id和角色关联
        * AND rm.`role_id` =r.`id` # 用户角色id和角色关联
        * AND rm.`menu_id` = m.`menu_id`  #权限和权限表关联
```

