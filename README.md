## 目录

### [使用framework-core框架](#使用framework-core框架-1)
#### &emsp;&emsp;&emsp;&emsp;[maven引入](#maven引入-1)

### [基本规范](#基本规范-1)
#### &emsp;&emsp;&emsp;&emsp;[接口出参](#接口出参-1)
#### &emsp;&emsp;&emsp;&emsp;[业务异常](#业务异常-1)
#### &emsp;&emsp;&emsp;&emsp;[entity文件](#entity文件-1)
#### &emsp;&emsp;&emsp;&emsp;[mapper文件](#mapper文件-1)
#### &emsp;&emsp;&emsp;&emsp;[service文件](#service文件-1)

## 正文

### 使用framework-core框架

#### maven引入

```xml
    <dependency>
      <groupId>com.vantfo.framework</groupId>
      <artifactId>core</artifactId>
      <version>1.1-SNAPSHOT</version>
    </dependency>
```

### 基本规范
使用framework-core框架需要在 **`接口出参、业务异常、entity文件、mapper文件、service文件`** 方面遵循基本的规范

#### 接口出参
controller层返回参数时需显示定义为[AjaxResult](/src/main/java/com/vantfo/framework/core/ajax/AjaxResult.java)，如：
``` java
	@GetMapping("/currentUserInfo")
    public AjaxResult getCurrentUserInfo() {
        UserInfoVO info = userService.getCurrentUserInfo();
        return AjaxResult.success(info);
    }
```
[AjaxResult](/src/main/java/com/vantfo/framework/core/ajax/AjaxResult.java)提供基本的success方法，在业务成功时使用：
``` java
return AjaxResult.success(data);
```
业务失败时需要在业务层显示抛出相关异常，见[业务异常](#业务异常-1)

#### 业务异常
业务中如果无法进行时（如参数校验失败）需显示抛出相关异常[BusinessException](/src/main/java/com/vantfo/framework/core/exception/BusinessException.java)，如：
``` java
 public UserLoginVO login(LoginDTO loginDTO) {
        UserInfo user = getUserInfo(loginDTO.getUsername());
        // 账号不存在或密码错误，提示"账号或密码错误"信息
        if(user == null || !passwordEncoder.matches(loginDTO.getPassword(), user.getPassword())) {
            throw new BusinessException(LoginExceptionInfo.SYS_USERNAME_OR_PASSWORD_ERROR);
        }
        // 业务代码
```
建议每项业务继承[BaseException](/src/main/java/com/vantfo/framework/core/exception/BaseException.java)创建业务的异常信息：
``` java
/**
 * 用户相关错误信息枚举类
 */
public enum LoginExceptionInfo implements BaseException {

    SYS_USERNAME_OR_PASSWORD_ERROR("SYS_USERNAME_OR_PASSWORD_ERROR", "账号或密码错误"),
    ;
    /**
     * 异常编码
     */
    private String errorCode;

    /**
     * 异常信息
     */
    private String errorMsg;
    // 实现errorCode、errorMsg get/set方法
}
```
#### entity文件

```java
@Data
// 使用@TableName注解标明表的名称
@TableName(value ="sys_user")
public class User implements Serializable {
    /**
     * 
     */
    @TableId
    private Long id;

    /**
     * 用户名
     */
    private String username;
    
    /**
     * 框架可自动转换驼峰，但如属性名跟表中字段不统一，可用@TableField注解标明表中的字段名称
     */
    @TableField("create_user_id")
    private Long createId;
    
    /**
     * 非表相关字段，使用@TableField(exist = false)注解标明
     */
    @TableField(exist = false)
    private static final long serialVersionUID = 1L;
    // 其它字段
}
```
建议使用mybatisx工具生成相关实体、mapper、service文件，安装使用步骤见[代码生成插件安装](/z_md/代码生成插件安装.md)

#### mapper文件
mapper文件需要继承[ExpandMapper](/src/main/java/com/vantfo/framework/core/mybatis/mapper/ExpandMapper.java)，如：
``` java
public interface UserMapper extends ExpandMapper<User> {

}
```
建议使用mybatisx工具生成相关实体、mapper、service文件，安装使用步骤见[代码生成插件安装](/z_md/代码生成插件安装.md)

mapper方法介绍见[mapper公共方法](/z_md/mapper公共方法.md)

#### service文件
service文件需要继承[BaseServiceImpl](/src/main/java/com/vantfo/framework/core/service/impl/BaseServiceImpl.java)，如：

``` java
@Service
public class UserServiceImpl extends BaseServiceImpl<UserMapper, User> implements UserService{
    // 业务代码
}
```
建议使用mybatisx工具生成相关实体、mapper、service文件，安装使用步骤见[代码生成插件安装](/z_md/代码生成插件安装.md)

service方法介绍见[service公共方法](/z_md/service公共方法.md)

