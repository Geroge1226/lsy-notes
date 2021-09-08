### 引言
`mybatis`在使用时，虽然可以使用`mybatis-generator`帮我们自动生成简单的增删改查模板类。但随着项目开发深入，免不了要新添加新字段，此时`generator`工具就不好使用，除非删除现有的所有文件重新生成（这里存在问题，对于手动调整过的sql删除文件时要摘出，文件生成后在录入，很容易出错！），通常会手动把新字段写到原来的所有增删改查的sql中。这会十分痛苦，而mybatis-plus就能帮我们解决问题。

### 1、说明
`Mybatis Plus`是国内*苞米豆(`baomidou`)*团队，在`Mybatis`的基础上开发的框架，在`Mybatis`基础上扩展了许多功能，但只是在`Mybatis`的基础上做了增强，却不做改变。
我们在使用`Mybatis-Plus`之后既可以使用`Mybatis-Plus`的特有功能，又能够正常使用`Mybatis`的原生功能。`Mybatis-Plus`是为简化开发、提高开发效率而生，但它也提供了一些很有意思的插件，比如：SQL性能监控、乐观锁、执行分析等。
>官网社区：https://mp.baomidou.com/guide/
### 2、使用实践
（1）准备数据库表：`sys_user`表结构
```sql
CREATE TABLE `sys_user` (
  `user_id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_code` varchar(100) DEFAULT NULL COMMENT '昵称',
  `real_name` varchar(20) DEFAULT NULL COMMENT '真实姓名',
  `password` varchar(200) DEFAULT NULL COMMENT '密码加密存储',
  `encrypt_password` blob COMMENT '加密字段',
  `gender` int(11) DEFAULT NULL COMMENT '性别 1-男 2-女',
  `nick_name` varchar(200) DEFAULT NULL COMMENT '昵称',
  `mobile` varchar(30) DEFAULT NULL COMMENT '手机号',
  `encrypt_mobile` varchar(128) DEFAULT NULL COMMENT '加密手机号',
  `email` varchar(50) DEFAULT NULL COMMENT '邮箱',
  `status` int(11) DEFAULT NULL COMMENT '状态，1-有效，0-无效',
  `create_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '注册时间',
  `update_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  `delete_time` datetime DEFAULT NULL COMMENT '删除时间',
  PRIMARY KEY (`user_id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8 COMMENT='系统用户表';
```
（2）现有`springboot `项目，结构如下：
![](https://upload-images.jianshu.io/upload_images/10817794-c3655f4cca2a64d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

（3）`pom`引用`mybatis-plus `依赖及数据库`jdbc`包
```
<properties>
        <mysql.connector.version>5.1.47</mysql.connector.version>
</properties>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>${mysql.connector.version}</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.baomidou/mybatis-plus-boot-starter -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.3.2</version>
</dependency>
```
（4）数据库配置文件
```yaml
spring:
  # 数据库配置
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/my-famliy
    name: root
    password: zzzzzz
```
`注`，这里配置`name`会报错，改为`username`。url 中后面增加ssl免校验，统一编码等。

（5）增加`mapper`类
```
@Mapper
public interface UserMapper extends BaseMapper<UserEntity> {
}
```
（6）数据库映射实体类（`UserEntity`）
```
@Data
@TableName("sys_user")
public class UserEntity {
    private String user_id;
    private String user_code;
    private String real_name;
    private String password;
    private String gender;
    private String nick_name;
    private String mobile;
    private String email;
    private String status;
    private String update_time;
    private String create_time;
}
```
（7）启动类增加扫描mapper注解`@MapperScan`
```
@Slf4j
@SpringBootApplication
@MapperScan({"com.lsy.family.dao"})
public class FamilyAdminApplication {

    public static void main(String[] args) {
        // 设置虚拟机时区
        TimeZone.setDefault(TimeZone.getTimeZone("Asia/Shanghai"));
        SpringApplication.run(FamilyAdminApplication.class, args);
        log.info("=======服务启动成功=====");
    }
```
（8）service类配置
```
public interface UserService {
    Object queryUserByMobile(String mobile);
    List  querAlluser();
}
```
```
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserMapper mapper;
    @Override
    public Object queryUserByMobile(String mobile) {
        return null;
    }
    @Override
    public List querAlluser() {
        List<UserEntity> userList = mapper.selectList(null);
        userList.stream().forEach(user-> System.out.println(user.getMobile()+user.getRealName()));
        return null;
    }
}
```
（9）controller层配置
```
@RestController
@RequestMapping("api/user")
@Api(value = "用户管理类")
public class UserController {
    @Autowired
    private UserService userService;

   @GetMapping("querAll")
    public Object fingUser(){
       List all =  userService.querAlluser();
       return null;
    }
}
```
- 启动项目访问看控制打印
```
2021-09-03 15:29:02.834 [INFO] [T: http-nio-8050-exec-1][tid：] [C:o.a.c.c.C.[.[.[/family-admin] ->log:173] | Initializing Spring DispatcherServlet 'dispatcherServlet'
2021-09-03 15:29:02.834 [INFO] [T: http-nio-8050-exec-1][tid：] [C:o.s.w.s.DispatcherServlet ->initServletBean:525] | Initializing Servlet 'dispatcherServlet'
2021-09-03 15:29:02.849 [INFO] [T: http-nio-8050-exec-1][tid：] [C:o.s.w.s.DispatcherServlet ->initServletBean:547] | Completed initialization in 14 ms
2021-09-03 15:29:02.988 [INFO] [T: http-nio-8050-exec-1][tid：] [C:c.z.h.HikariDataSource ->getConnection:110] | HikariPool-1 - Starting...
2021-09-03 15:29:03.282 [INFO] [T: http-nio-8050-exec-1][tid：] [C:c.z.h.HikariDataSource ->getConnection:123] | HikariPool-1 - Start completed.
184535龙*x
137777李
1233张*
```
至此，已经初步使用 `mybatis-plus `实现