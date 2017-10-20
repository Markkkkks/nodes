# 过滤器和包装器

## 过滤器

用于请求发送到servlet之前拦截和处理请求，并可在servlet结束工作之后用过滤器处理响应。

容器根据DD中的声明来确定和事调用过滤器。

### 过滤器的生命周期

每个过滤器要实现以下方法

- init()
- doFilter()

     `doFilterServletRequest res,ServletResponse resp,FilterChain chain）`中参数3为FilterChain(过滤器链条)，一般情况下，调用该方法内包含`filterChain.doFilter(request,response)`实现栈式调用，调用顺序由DD决定。

- destroy()



### 声明过滤器

在dd中声明

```xml
<filter>
<filter-name/>
<filter-class/>
<init-param>(可选)
</filter>
```

#### 声明对应url模式的过滤器映射

```xml
<filter-mapping>
<filter-name/>
<url-pattern/>
</filter-mapping>
```

#### 声明对应servlet名的过滤器映射

```xml
<filter-mapping>
<filter-name>
<servlet-name>
</filter-mapping>
```

### 确定过滤器顺序

当多个过滤器影射到一个给定资源时，容器使用以下规则

1. 先找到url匹配的所有过滤器。客户做出一个资源请求时，容器会采用URL映射规则选择适合资源，简言之由dd上的顺序优先，并由filterchain穿成链
1. 与serlvet－name完全匹配的filter放在链的末端
