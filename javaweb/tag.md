# 自定义标签的使用

本笔记用于记录开发javaweb过程中有可能使用到的自定义标签(将jsp中的java代码实现的功能封装到tag中)的自定义和使用的知识。

## tag标签的入门使用

### tag接口

tag接口中提供了以下方法

- `public void setPageContext(PageContext pc)`

    该方法属于标签初始化阶段，用于传递调用该标签的jsp的pagecontext对象，并且从pagecontext对象中可以获取到jsp的其他工具用于实现标签的开发。
- `public void setParent(Tag t)`

    该方法属于标签初始化阶段，用于设定该自定义标签的上一层标签，便于开发使用
- `public int doStartTag()`
- `public int doEndTag()`

    `doStart()` 与`doEnd()`两个两个方法都可以用于处理开发过程中该标签需要实现的逻辑
- `public void release()`

    释放标签时使用
- `public Tag getParent()`

另外地，想实现一个简单的自定义标签，可以通过使类继承`SimpleTagSupport`实现。该类详细使用方法在[SimpleTagSupport](https://www.tutorialspoint.com/jsp/jsp_custom_tags.htm)   上可以看到

### 使用自定义标签的步骤

1. 创建自定义标签类 `implements Tag`

    在该标签内可以通过setPageContext体内获取到jsp的对象中所有工具，用于out等

1. 在`WEB-INF`文件旁放置`custom.tld`文件，并在文件内键入以下信息
    ```java
    <taglib>
   <tlib-version>1.0</tlib-version>
   <jsp-version>2.0</jsp-version>
   <short-name>Example TLD</short-name>
   <uri>/testuri</uri>
   <tag>
      <name>Hello</name>
      <tag-class>com.tutorialspoint.HelloTag</tag-class>
      <body-content>empty</body-content>
   </tag>
</taglib>
    ```

1. 在jsp中的头加上`<%@ taglib prefix = "ex" uri = "WEB-INF/custom.tld"%>`或者uri中的网址选择为taglib标签中的lib，即可绑定自定义标签，开始使用。

### 自定义标签内容扩展

可扩展的功能：

- 控制jsp页面某一部分是否执行
- 控制jsp整个页面是否执行
- 控制jsp页面内容重复执行
- 修改jsp页面内容输出

#### 控制jsp页面某一部分是否执行

 doStartTag()的扩展功能

文档原文：

```shell
This method returns Tag.EVAL_BODY_INCLUDE or BodyTag.EVAL_BODY_BUFFERED to indicate that the body of the action should be evaluated or SKIP_BODY to indicate otherwise.

When a Tag returns EVAL_BODY_INCLUDE the result of evaluating the body (if any) is included into the current "out" JspWriter as it happens and then doEndTag() is invoked.

BodyTag.EVAL_BODY_BUFFERED is only valid if the tag handler implements BodyTag.
```

1. 返回值为`EVAL_BODY_INCLUDE`的话，标签中的内容会输出到out中
1. 返回值为`SKIP_BODy`的话，标签体会被忽略
