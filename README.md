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

使用异步任务管理器，结合线程池，实现了异步的操作日志记录，和业务逻辑实现异步解耦合。

## getInfo

`*:*:*`这个是获取所有的权限

获取当前用户的角色和权限信息，存储到 Vuex 中

![image-20220710231603773](C:\learning\项目\RuoYi-Vue\images\image-20220710231603773.png)

![image-20220710231644203](C:\learning\项目\RuoYi-Vue\images\image-20220710231644203.png)

## getRouters

根据当前用户的权限获取动态路由

![image-20220710231804688](C:\learning\项目\RuoYi-Vue\images\image-20220710231804688.png)

# 日志

## 登陆日志

## 操作日志

p6



