# servlet中关于session的总结

1.session 的创立

`request.getSession` 效果为如果无session创建该session 并且对客户端发送cookie，cookie中记录了session的`jsessionid`，该id用于发送给服务器获取到对应的session

该session创建时经历了以下步骤：

```shell
新建session对象
生成会话id
建立cookie
关联id到cookie
响应中设置Cookie
```

2.客户端禁用cookie解决方案

客户端禁用cookie时，容器依赖url重写,重写的思路是同时发送`jsessionid`和cookie，如果有发送cookie，此后不再发送`jsessionid`，反之只发送id；

```java
response.encodeURL(request.getContextPath());
```

3.session存活时间等参数的修改

```shell
30分钟没有使用session，自动销毁session
web.xml中手工配置销毁时间<session-config><session-timeout>
手工调用`session.invalidate`方法改变时间
```

4.利用session创建登陆验证逻辑taken

```shell
在提交表单的页面生成一个token，并且把token以hidden标签发送一次，session中寸token发送一次。在服务器端获取 session 中的token与form中的token进行逻辑处理
1.input中token为空 用户不是用服务器给的html进行访问servlet 返回false；
2.session 中token为空 多次访问
3.确认session中的token与input中token不相同
4.return true；
```

5.利用session验证验证码

```shell
生成验证码的同时在session中保存，当需要验证时候取出判断两者是否非空，并且判断是否相同。
```