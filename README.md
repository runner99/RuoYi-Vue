# RuoYi-Vue
若依前后端分离3.3.0

# 验证码实现

![image-20220703212340407](C:\learning\项目\RuoYi-Vue\images\image-20220703212340407.png)

# poi使用

![image-20220704124058276](C:\learning\项目\RuoYi-Vue\images\image-20220704124058276.png)

![image-20220704124041771](C:\learning\项目\RuoYi-Vue\images\image-20220704124041771.png)



# 用户角色权限

![image-20220705122151538](C:\learning\项目\RuoYi-Vue\images\image-20220705122151538.png)

# 前端验证码

后端生成一个表达式，1+1=2

1+1=?@2

1+1=? 转成图片，传到前端进行展示

2 存入 Reids

redis的key是生成的一个uuid

前端：http://localhost/dev-api/captchaImage 

Vue 获取图片，前端还是后端？

反向代理，url 请求前端，进行代理，映射到后端，解决跨越问题

![image-20220710220121439](C:\learning\项目\RuoYi-Vue\images\image-20220710220121439.png)

# 登陆的实现

## login

前端：请求 URL: http://localhost/dev-api/login

![image-20220710222548283](C:\learning\项目\RuoYi-Vue\images\image-20220710222548283.png)

![image-20220710222557897](C:\learning\项目\RuoYi-Vue\images\image-20220710222557897.png)

![image-20220710222606189](C:\learning\项目\RuoYi-Vue\images\image-20220710222606189.png)

后端：

1.校验验证码

2.校验用户名和密码

3.生成Token

使用异步任务管理器，结合线程池，实现了异步的登陆日志记录，和业务逻辑实现异步解耦合。

## getInfo

`*:*:*`这个是获取所有的权限

获取当前用户的角色和权限信息，存储到 Vuex 中

![image-20220710231603773](C:\learning\项目\RuoYi-Vue\images\image-20220710231603773.png)

![image-20220710231644203](C:\learning\项目\RuoYi-Vue\images\image-20220710231644203.png)

## getRouters

根据当前用户的权限获取动态路由

![image-20220710231804688](C:\learning\项目\RuoYi-Vue\images\image-20220710231804688.png)

# 系统管理

## 用户管理

### 1.list

![image-20220711101921581](C:\learning\项目\RuoYi-Vue\images\image-20220711101921581.png)

![image-20220711102009708](C:\learning\项目\RuoYi-Vue\images\image-20220711102009708.png)

![image-20220711102230885](C:\learning\项目\RuoYi-Vue\images\image-20220711102230885.png)

### 2.treeselect

![image-20220711101942819](C:\learning\项目\RuoYi-Vue\images\image-20220711101942819.png)

# 事务

同SpringBoot，主要注解：@Transactional

- 常见坑点1：遇到检查异常时，事务开启，也无法回滚。 例如下面这段代码，用户依旧增加成功，并没有因为后面遇到检查异常而回滚！！

![image-20220711162150887](C:\learning\项目\RuoYi-Vue\images\image-20220711162150887.png)

原因分析：因为`Spring`的默认的事务规则是遇到运行异常`（RuntimeException）`和程序错误`（Error）`才会回滚。如果想针对检查异常进行事务回滚，可以在`@Transactional`注解里使用 `rollbackFor`属性明确指定异常。
例如下面这样，就可以正常回滚：

![image-20220711162203504](C:\learning\项目\RuoYi-Vue\images\image-20220711162203504.png)

- 常见坑点2： 在业务层捕捉异常后，发现事务不生效。 这是许多新手都会犯的一个错误，在业务层手工捕捉并处理了异常，你都把异常“吃”掉了，`Spring`自然不知道这里有错，更不会主动去回滚数据。
  例如：下面这段代码直接导致用户新增的事务回滚没有生效。

![image-20220711162235938](C:\learning\项目\RuoYi-Vue\images\image-20220711162235938.png)

推荐做法：在业务层统一抛出异常，然后在控制层统一处理。

![image-20220711162249280](C:\learning\项目\RuoYi-Vue\images\image-20220711162249280.png)

|               |                                                              |
| ------------- | ------------------------------------------------------------ |
| 属性          | 说明                                                         |
| propagation   | 事务的传播行为，默认值为 REQUIRED。                          |
| isolation     | 事务的隔离度，默认值采用 DEFAULT                             |
| timeout       | 事务的超时时间，默认值为-1，不超时。如果设置了超时时间(单位秒)，那么如果超过该时间限制了但事务还没有完成，则自动回滚事务。 |
| read-only     | 指定事务是否为只读事务，默认值为 false；为了忽略那些不需要事务的方法，比如读取数据，可以设置 read-only 为 true。 |
| rollbackFor   | 用于指定能够触发事务回滚的异常类型，如果有多个异常类型需要指定，各类型之间可以通过逗号分隔。{xxx1.class, xxx2.class,……} |
| noRollbackFor | 抛出 no-rollback-for 指定的异常类型，不回滚事务。{xxx1.class, xxx2.class,……} |
| ....          |                                                              |

> 提示
>
> 事务的传播机制是指如果在开始当前事务之前，一个事务上下文已经存在，此时有若干选项可以指定一个事务性方法的执行行为。 即:在执行一个@Transactinal注解标注的方法时，开启了事务；当该方法还在执行中时，另一个人也触发了该方法；那么此时怎么算事务呢，这时就可以通过事务的传播机制来指定处理方式。

| 常量                                            | 含义                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| TransactionDefinition.PROPAGATION_REQUIRED      | 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。这是默认值。 |
| TransactionDefinition.PROPAGATION_REQUIRES_NEW  | 创建一个新的事务，如果当前存在事务，则把当前事务挂起。       |
| TransactionDefinition.PROPAGATION_SUPPORTS      | 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。 |
| TransactionDefinition.PROPAGATION_NOT_SUPPORTED | 以非事务方式运行，如果当前存在事务，则把当前事务挂起。       |
| TransactionDefinition.PROPAGATION_NEVER         | 以非事务方式运行，如果当前存在事务，则抛出异常。             |
| TransactionDefinition.PROPAGATION_MANDATORY     | 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。 |
| TransactionDefinition.PROPAGATION_NESTED        | 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。 |

# 异常处理

全局异常处理。通过一个全局异常处理类来进行处理GlobalExceptionHandler，这里面有两个重要的注解叫：@RestControllerAdvice，@ExceptionHandler(BaseException.class)，用AOP的方法去增强方法，若发生异常将由ExceptionHandler注解的方法去处理这个异常，一般都是给前端返回一个错误的信息。这里BaseException.class就是指定某个异常，当这个异常发生，就会执行相关方法，例如：

```
/**
 * 基础异常
 */
@ExceptionHandler(BaseException.class)
public AjaxResult baseException(BaseException e)
{
    return AjaxResult.error(e.getMessage());
}
```

![image-20220711223809717](C:\learning\项目\RuoYi-Vue\images\image-20220711223809717.png)

# 权限注解

```
1、xxx：xxx：list意思是能不能进入这个菜单进行对表增删改查，如果没有这个权限，前端不能进入菜单。
2、xxx：xxx：add，remove，edit，query，意思是对前端的增删改查实现，往往会在前端的组件多加一个指令v-hasPermi（若依封装），如果没有这个权限，则组件将不会显示。

3、后端执行某个controller若需要某个权限才能执行，则需要加上注解@PreAuthorize("@ss.hasPermi('system:user:list')")
这f个注解是若依+SpringSecurity框架共同实现的，它会先去执行方法ss.hasPermi('system:user:list')来判断是否能去执行相关的controller。
4、在登录的时候，每次点击全局页面刷新的时候，都会执行getInfo方法去获取权限，这个权限会被vuex进行管理，从而方便在每一个界面判断是否展示某个组件/按钮。
```

![image-20220712092838083](C:\learning\项目\RuoYi-Vue\images\image-20220712092838083.png)

登陆的时候的getInfo里面获取可操作的权限

![image-20220712092411424](C:\learning\项目\RuoYi-Vue\images\image-20220712092411424.png)

# 数据权限

所谓数据权限，就是某个用户能查询哪些数据，来看一下若依给我们的解释：

在实际开发中，需要设置用户只能查看哪些部门的数据，这种情况一般称为数据权限。
 例如对于销售，财务的数据，它们是非常敏感的，因此要求对数据权限进行控制， 对于基于集团性的应用系统而言，就更多需要控制好各自公司的数据了。如设置只能看本公司、或者本部门的数据，对于特殊的领导，可能需要跨部门的数据， 因此程序不能硬编码那个领导该访问哪些数据，需要进行后台的权限和数据权限的控制。

针对角色设置的一个权限，自定义一个注解，然后像log一样对其进行切入

![image-20220712100415120](C:\learning\项目\RuoYi-Vue\images\image-20220712100415120.png)

```java
/**
 * 数据过滤处理
 *
 * @author ruoyi
 */
@Aspect
@Component
public class DataScopeAspect
{
    /**
     * 全部数据权限
     */
    public static final String DATA_SCOPE_ALL = "1";

    /**
     * 自定数据权限
     */
    public static final String DATA_SCOPE_CUSTOM = "2";

    /**
     * 部门数据权限
     */
    public static final String DATA_SCOPE_DEPT = "3";

    /**
     * 部门及以下数据权限
     */
    public static final String DATA_SCOPE_DEPT_AND_CHILD = "4";

    /**
     * 仅本人数据权限
     */
    public static final String DATA_SCOPE_SELF = "5";

    /**
     * 数据权限过滤关键字
     */
    public static final String DATA_SCOPE = "dataScope";

    // 配置织入点
    @Pointcut("@annotation(com.ruoyi.common.annotation.DataScope)")
    public void dataScopePointCut()
    {
    }

    @Before("dataScopePointCut()")
    public void doBefore(JoinPoint point) throws Throwable
    {
        handleDataScope(point);
    }

    protected void handleDataScope(final JoinPoint joinPoint)
    {
        // 获得注解
        DataScope controllerDataScope = getAnnotationLog(joinPoint);
        if (controllerDataScope == null)
        {
            return;
        }
        // 获取当前的用户
        LoginUser loginUser = SpringUtils.getBean(TokenService.class).getLoginUser(ServletUtils.getRequest());
        if (StringUtils.isNotNull(loginUser))
        {
            SysUser currentUser = loginUser.getUser();
            // 如果是超级管理员，则不过滤数据
            if (StringUtils.isNotNull(currentUser) && !currentUser.isAdmin())
            {
                dataScopeFilter(joinPoint, currentUser, controllerDataScope.deptAlias(),
                        controllerDataScope.userAlias());
            }
        }
    }

    /**
     * 数据范围过滤
     *
     * @param joinPoint 切点
     * @param user 用户
     * @param userAlias 别名
     */
    public static void dataScopeFilter(JoinPoint joinPoint, SysUser user, String deptAlias, String userAlias)
    {
        StringBuilder sqlString = new StringBuilder();

        for (SysRole role : user.getRoles())
        {
            String dataScope = role.getDataScope();
            if (DATA_SCOPE_ALL.equals(dataScope))
            {
                sqlString = new StringBuilder();
                break;
            }
            else if (DATA_SCOPE_CUSTOM.equals(dataScope))
            {
                sqlString.append(StringUtils.format(
                        " OR {}.dept_id IN ( SELECT dept_id FROM sys_role_dept WHERE role_id = {} ) ", deptAlias,
                        role.getRoleId()));
            }
            else if (DATA_SCOPE_DEPT.equals(dataScope))
            {
                sqlString.append(StringUtils.format(" OR {}.dept_id = {} ", deptAlias, user.getDeptId()));
            }
            else if (DATA_SCOPE_DEPT_AND_CHILD.equals(dataScope))
            {
                sqlString.append(StringUtils.format(
                        " OR {}.dept_id IN ( SELECT dept_id FROM sys_dept WHERE dept_id = {} or find_in_set( {} , ancestors ) )",
                        deptAlias, user.getDeptId(), user.getDeptId()));
            }
            else if (DATA_SCOPE_SELF.equals(dataScope))
            {
                if (StringUtils.isNotBlank(userAlias))
                {
                    sqlString.append(StringUtils.format(" OR {}.user_id = {} ", userAlias, user.getUserId()));
                }
                else
                {
                    // 数据权限为仅本人且没有userAlias别名不查询任何数据
                    sqlString.append(" OR 1=0 ");
                }
            }
        }

        if (StringUtils.isNotBlank(sqlString.toString()))
        {
            Object params = joinPoint.getArgs()[0];
            if (StringUtils.isNotNull(params) && params instanceof BaseEntity)
            {
                BaseEntity baseEntity = (BaseEntity) params;
                baseEntity.getParams().put(DATA_SCOPE, " AND (" + sqlString.substring(4) + ")");
            }
        }
    }

    /**
     * 是否存在注解，如果存在就获取
     */
    private DataScope getAnnotationLog(JoinPoint joinPoint)
    {
        Signature signature = joinPoint.getSignature();
        MethodSignature methodSignature = (MethodSignature) signature;
        Method method = methodSignature.getMethod();

        if (method != null)
        {
            return method.getAnnotation(DataScope.class);
        }
        return null;
    }
}

```

![image-20220712100817700](C:\learning\项目\RuoYi-Vue\images\image-20220712100817700.png)



# 日志

## 异步任务管理器

### 登陆日志

![image-20220711151815410](C:\learning\项目\RuoYi-Vue\images\image-20220711151815410.png)

通过异步任务管理器记录登录日志

1、AsyncManager.me() 获取一个 AsycnManager 对象

2、执行 execute 方法，执行任务，传入的是一个 Task对象，实现了 Runnable 接口，是一个任务，由线程Thread 去执行



封装了登录用户的信息，执行添加操作，这里不会执行，而是将任务交给线程对象来执行。

异步任务管理器，内部定义了一个线程池，然后根据业务创建添加日志的任务，交给线程池来处理，这样做到日志和业务的抽象，解耦合，日志全部统一处理。

## 操作日志

控制器操作日志

- AOP切面实现逻辑-debug。
- 也用了异步任务管理器，内部定义了一个线程池，然后根据业务创建添加日志的任务
- 案例-自定义自己的操作日志。

在实际开发中，对于某些关键业务，我们通常需要记录该操作的内容，一个操作调一次记录方法，每次还得去收集参数等等，会造成大量代码重复。 我们希望代码中只有业务相关的操作，在项目中使用注解来完成此项功能。

在需要被记录日志的`controller`方法上添加`@Log`注解，使用方法如下：

```java
@Log(title = "用户管理", businessType = BusinessType.INSERT)
public AjaxResult addSave(...)
{
    return success(...);
}
```

### 注解参数说明

| 参数              | 类型         | 默认值 | 描述                                                         |      |      |
| ----------------- | ------------ | ------ | ------------------------------------------------------------ | ---- | ---- |
| title             | String       | 空     | 操作模块                                                     |      |      |
| businessType      | BusinessType | OTHER  | 操作功能（`OTHER`其他、`INSERT`新增、`UPDATE`修改、`DELETE`删除、`GRANT`授权、`EXPORT`导出、`IMPORT`导入、`FORCE`强退、`GENCODE`生成代码、`CLEAN`清空数据） |      |      |
| operatorType      | OperatorType | MANAGE | 操作人类别（`OTHER`其他、`MANAGE`后台用户、`MOBILE`手机端用户） |      |      |
| isSaveRequestData | boolean      | true   | 是否保存请求的参数

```java
/**
 * 自定义操作日志记录注解
 * 
 * @author ruoyi
 *
 */
@Target({ ElementType.PARAMETER, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Log
{
    /**
     * 模块 
     */
    public String title() default "";

    /**
     * 功能
     */
    public BusinessType businessType() default BusinessType.OTHER;

    /**
     * 操作人类别
     */
    public OperatorType operatorType() default OperatorType.MANAGE;

    /**
     * 是否保存请求的参数
     */
    public boolean isSaveRequestData() default true;
}
```

### AOP进行切入

![image-20220711230359018](C:\learning\项目\RuoYi-Vue\images\image-20220711230359018.png)、

![image-20220711230732425](C:\learning\项目\RuoYi-Vue\images\image-20220711230732425.png)

# 代码自动生成

## 1、创建数据表

```sql
use ruoyi_vue;
create table test_user(
id int primary key auto_increment,
name varchar(11),
password varchar(11)
);
```

## 2、系统工具 -》代码生成

![image-20220711154033277](C:\learning\项目\RuoYi-Vue\images\image-20220711154033277.png)

## 3、编辑

![image-20220711154044770](C:\learning\项目\RuoYi-Vue\images\image-20220711154044770.png)

## 4、点击生成代码

## 5、解压

main（Java 后端代码）

vue（Vue 前端代码）

SQL（菜单 SQL）

## 6、导入代码，重启项目

如果后端抛出 404 异常，rebuild project，重新启动即可



