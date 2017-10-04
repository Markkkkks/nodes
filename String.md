# java基础


## String

---------------------

### 不可变类

String 对象是不可变的，对String类的操作实际上都是创建了一个全新的String对象，查看文档中的String类名如下

```java
public final class String
extends Object
implements Serializable, Comparable<String>, CharSequence
```

由于String对象是final 不可变类，因此将String传入方法时，实际传递的是引用的一个拷贝，每当传入的函数对目标引用进行各种转换如`upcase()`等，改变的是局部引用，该局部引用已经指向了一个新的对象，传入方法的应用并无改变。

---------------------

### 重载的+= 与+

String的不可变性会带来一定的效率问题，为String对象重载的"+"与“+=”就是一个例子。

参考代码`String mango = "mango";String s = "abc" + mango + "def" + 47;`该句代码的底层（汇编）操作如下：


- 对+自动创建StringBuilder对象

- 共调用4次StringBuilder 中的append()方法

- 最后调用toString()产生结果

虽然编译器可以自动优化性能，但是通过下列代码的对比，我们可以看出编译器的优化程度是有限的，下面两个方法同目的为生成一个String，方法1使用了多个String对象，方法2在代码中使用了Stringbuilder;

```java
public class WhitherStringBuilder{
    public String implicit(String[] fields){
        String result = "";
        for(int i = 0;i<fields.length;i++)
            result +=fields[i];
        return result;
    }
    public String explicit(String[]fields){
        String result = new StringBuilder();
        for(int i = 0;i<fields.length;i++)
            result.append(fields[i]);
        return result;
    }
}
```

两个方法编译器对应的操作分别如下



1.方法一每次循环中的操作如下

- 创建StringBuilder
- StringBuilder.append()
- StringBuilder.toString


2.方法2中整个函数的操作如下

- 创建StringBuilder
- 循环内append()
- 循环外toString

可以看到循环部分代码更简单，且只生成一个StringBuilder对象，更大好处是可预先制定StringBuilder的大小  ps:java1.5前使用的是StringBuffer，
java1.5后使用的是StringBuilder，
展开对比区别。

---------------------

### 无意识递归调用

案例代码如下：

```java
    public class InfiniteRecursion{
        public String toString(){
            return " InfiniteRecursion address:" + this +" /n ";
        }
        public tatic void main(String []args){
            List<InfiniteRecursion> v = new ArrayList<InfiniteRecursion>();
            System.out.println(v);
        }
    }
```

该代码的执行会报异常，调用toString的过程发生了自动类型转换，字符串后面 + 跟着的this会被尝试转换成String，因此this会被尝试调用this.toStirng形成递归调用，若仍想解决递归调用，应该用super.toString()，返回结果是this的地址。

---------------------

### String中常用的基本方法

`charAt();位置对应字符
equalsIgnoreCase();无视大小写匹配
lastIndexOf();最后出现字符
substring();
concat();字符串链接
intern();为每个唯一的字符序列生成一个且仅生成一个String引用`

---------------------

### 正则表达式的使用

java.util.regex  一个完全开放、自带的正则表达式包
正则表达式中由于java.util.regex不支持反斜杠"/"的解读
因此需要在真正赋值给String的时候使用双反斜杠

#### 正则表达式的语法

所有语法可在`java.util.regex.Pattern`文档中查看

类别

字符类

- \d    A digit: [0-9]
- \D    A non-digit: [^0-9]
- \s    A whitespace character: [ \t\n\x0B\f\r] 空格间隙相关
- \S    A non-whitespace character: [^\s]
- \w    A word character: [a-zA-Z_0-9]
- \W    A non-word character: [^\w]

---------------------

贪婪性量词 Greedy  quantifiers

- X?    X, once or not at all 一个或没有
- X*    X, zero or more times 0到无数个
- X+    X, one or more times 1到多个
- X{n}  X, exactly n times
- X{n,} X, at least n times n个以上
- X{n,m}    X, at least n but not more than m times n-m次

---------------------

勉强型量词   Reluctant quantifiers

- X??    X, once or not at all
- X*?    X, zero or more times
- X+?    X, one or more times
- X{n}?    X, exactly n times
- X{n,}?    X, at least n times
- X{n,m}?    X, at least n but not more than m times

---------------------

占有型量词 Possessive quantifiers

- X?+    X, once or not at all
- X*+    X, zero or more times
- X++    X, one or more times
- X{n}+    X, exactly n times
- X{n,}+    X, at least n times
- X{n,m}+    X, at least n but not more than m times

---------------------

三者之间的区别 贪婪型量词：量词总是贪婪的，除非由其他的选项被设置。
勉强型：问号制定，这个量词匹配满足模式所需的最少字符数。
占有型：正则表达式匹配时会产生多种状态，匹配失败时可以回溯。占有型量词不保存这些中间状态，因此常用于防止正则表达式失控，保证执行效果

---------------------

分组 Group number

Capturing groups are numbered by counting their opening parentheses from left to right. In the expression ((A)(B(C))), for example, there are four such groups:

- 1       ((A)(B(C)))
- 2       (A)
- 3   (B(C))
- 4   (C)

这些组可以用 $n 来表示 比如第一组用 $1 来表示。

另外的，表达式X通常要用()括起来，比如`abc+`匹配的不是多个abc，而是ab后跟着1或者多个c，要达到效果应该用表达式`(abc)+`表示

#### 应用1  字符串匹配

##### SimpleDemo

- (nnn)nnn-nnnn = (/d{3})/s/d{3}-/d{4}
- 5~15数字 0不能开头 [1-9]/d{4-14}
- 手机 13xxx 15xxx 18xxx 1[358]/d{9}


#### 匹配方法

1.整段字符串匹配  `String.matches(Regex);`
2. 一大段文字去匹配

`pattern.compile(Regex);`  用模式绑定正则
`Matcher matcher = pattern.matcher(s);`然后获取模式匹配器和字符

常用到的函数`matcher.find(n);从第n个字符开始匹配。matcher.find()；任意位置开始匹配;matcher.lookingAt()输入第一部分匹配`

`matcher.start()匹配到的第一个字符位置,matcher.end()匹配到的最后字符位置`

代码如下

``` Java
    BufferedReader in;
　　Pattern pattern = Pattern.compile("\\(\\d{3}\\)\\s\\d{3}-\\d{4}");
　　in = new BufferedReader(new FileReader("phone"));
　　String s;
　　while ((s = in.readLine()) != null){
　　  Matcher matcher = pattern.matcher(s);
　　  if (matcher.find())
　　      System.out.println(matcher.group());
　　}
　　in.close();
 ```

#### 应用2 字符串分割

```java

String regex=" +";//多个空格
String testString ="sdf  wer w gwedfgh";//测试字符串
String []ss = testString.split(regex);

```

#### Demo1

- 迭词如 kk `(.)\\1`
- 多个空格 `( +)`






#### 应用3 字符串替换

```Java

    String newString =oldString.replaceAll(reg,newstr);

```


#### Demo("reg","newstr")

- 叠词替换成& `"(.)\\1+","&"`
- 叠词替换成单字 `"(.)\\1+","$1"`


#### 应用4 获取

``` Java

    BufferedReader in;
　　Pattern pattern = Pattern.compile("\\(\\d{3}\\)\\s\\d{3}-\\d{4}");

　　in = new BufferedReader(new FileReader("phone"));

　　String s;

　　while ((s = in.readLine()) != null)

　　{

　　Matcher matcher = pattern.matcher(s);

　　while (matcher.find())

　　{

　　System.out.println(matcher.group());//输出匹配到的字符串[类似于 020 123-1234 格式的电话号码]

　　}

　　}

　　in.close();

 ```

#### 综合应用


- ip地址排序"ip1 ip2 ip3 ip4 ip5"
- ip添加2个0 `replaceall("(\\d+)","00$1")`
- ip删除多余0(结果为三位ip) `replaceall("0*(\\d{3})","$1")`
- 分割 `split(" +")`
- 输出 `replaceall("0*(\\d+)","$1")`
- 邮件地址:reg="[a-zA-Z0-9_]@[a-zA-Z0-9]+\\.[a-zA-Z]"




## 扩展

- String.intern 与String常量池
- String.equals 与 == 与 String.intern()

### intern

`public String intern()`

返回字符串对象的规范化表示形式。

一个初始时为空的字符串池，它由类 String 私有地维护。

当调用 intern 方法时，如果池已经包含一个等于此 String 对象的字符串（该对象由 equals(Object) 方法确定），则返回池中的字符串。否则，将此 String 对象添加到池中，并且返回此 String 对象的引用。

它遵循对于任何两个字符串 s 和 t，当且仅当 s.equals(t) 为 true 时，s.intern() == t.intern() 才为 true。

所有字面值字符串和字符串赋值常量表达式都是内部的。

返回：

一个字符串，内容与此字符串相同，但它保证来自字符串池中。

eg:

```java
String str1 = "a";
String str2 = "b";
String str3 = "ab";
String str4 = str1 + str2;
String str5 = new String("ab");
System.out.println(str5.equals(str3));
System.out.println(str5 == str3);
System.out.println(str5.intern() == str3);
System.out.println(str5.intern() == str4);
```

输出： true false true false

原因：一般情况下只要是定义`String x = "";`定义的字符串(静态字符串)都会在常量池中，而且字符串相加的时候，只要相加字符串是静态的如`"a"+"b"`都会存在常量池中，new String这种new创建出来的字符串不进入常量池，因此`new String`与静态字符串的`==`返回值是false

ps:`equals`只要字符串是一样的就返回true,`==`必须指向同一地址的字符串

### 字符串常量池

在 JAVA 语言中有8中基本类型和一种比较特殊的类型String。这些类型为了使他们在运行过程中速度更快，更节省内存，都提供了一种常量池的概念。常量池就类似一个JAVA系统级别提供的缓存。
      8种基本类型的常量池都是系统协调的，String类型的常量池比较特殊。它的主要使用方法有两种：
1）直接使用双引号声明出来的String对象会直接存储在常量池中。
2）如果不是用双引号声明的String对象，可以使用String提供的intern方法。intern 方法会从字符串常量池中查询当前字符串是否存在，若不存在就会将当前字符串放入常量池中

eg:`String s = new String("abc")`共创建两个对象 `"abc"`在常量池创建了abc常量，并在堆Heap中创建了字符串

---------------------

- 常量池表（constant_pool table）

Class文件中存储所有常量（包括字符串）的table
这是Class文件中的内容，还不是运行时的内容，不要理解它是个池子，其实就是Class文件中的字节码指令

- 运行时常量池（Runtime Constant Pool）

JVM内存中方法区的一部分，这是运行时的内容
这部分内容（绝大部分）是随着JVM运行时候，从常量池`constant_pool table`转化而来，每个Class对应一个运行时常量池
2中说绝大部分是因为：除了 Class中常量池内容，还可能包括动态生成并加入这里的内容

eg: `int a = 1;String b = "asd";`

首先，1和"asd"会在经过javac（或者其他编译器）编译过后变为Class文件中constant_pool table的内容

当我们的程序运行时，也就是说JVM运行时，每个Classconstant_pool table中的内容会被加载到JVM内存中的方法区中各自Class的Runtime Constant Pool

一个没有被String Pool包含的Runtime Constant Pool中的字符串（这里是"asd"）会被加入到String Pool中（HosSpot使用hashtable引用方式），步骤如下：

在Java Heap中根据"asd"字面量create一个字符串对象
将字面量"asd"与字符串对象的引用在hashtable中关联起来，键 - 值 形式是："asd" = 对象的引用地址

---------------------

