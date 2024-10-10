### 角色权限

用到了5张表，分别是router【路由】、role【角色】、role_router【角色的路由】、role_user【角色下的用户】、user【用户】

> 关联步骤

用户和用户角色关联（该用户对应的角色，这里并没有关联角色只是查询出来）

用户角色和角色关联（这里才是关联上）

角色和角色路由关联（确认该角色的路由）

角色路由和路由（查询出路由的详细信息）

```sql
-- 根据用户名查询路由信息
SELECT * FROM `user` u,`role_user` ru,`role_router` rr,`role` r,`router` 
         WHERE u.`user_name` = "wang"  #用户名称
         AND u.`user_id` = ru.`user_id` # 用户id和角色用户表关联，查出该用户对应的角色
         AND ru.`role_id` = r.`role_id` # 用户角色id和角色关联
         AND rr.`role_id` =ru.`role_id` # 用户角色id和角色关联
         AND rr.`router_id` = router.`router_id`  #权限和权限表关联
```







### computed和watch的区别

computed当响应式数据发生改变时，会重新计算该响应式数据

watch是监听函数，当一个当响应式数据发生改变时回调函数会被调用，会提供newValue` 和 `oldValue

> 区别

computed：效率高 

watch：提供监听前后的数值，可以监听多个





### .then和await等待的区别

**并发环境下**

使用 then 是非阻塞的，允许并发执行

使用 await 会阻塞当前的 async 函数



