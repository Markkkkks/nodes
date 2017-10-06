# java i/0系统

## File类

File类既能代表特定文件的名称，也能代表一个目录下的一组文件的名称，如果指定的是文件集(`isDirectory()`)，可以对其使用list()方法，返回字符数组。

### 目录列表器

查看目录列表，可以通过调用不添加参数的list()方法，或者想获得受限的如想得到所有扩展名为java的文件。需要用到目录过滤器。

其实现方法为传入接口FilenameFilter;

```java
    public interface FilenameFilter{
        boolean accept(File dir,String name);
    }
```

eg:实例代码

```java
    File path = new File(".");//当前路径
    list = path.list(new FilenameFilter(){
        private Pattern pattern = Pattern.compile(regix);//外部类的正则表达式
        public boolean accept(File dir,String name){
            return pattern.matcher(name).matches();
        }
    })
```

通过传入的正则表达式，判断list返回结果中的每个文件，是否都满足正则，并返回正则的list。



## 在i/o中经常出现的编码问题解决方案：

JVM里面的任何字符串资源都是Unicode,String相当于 char[] 。 而JVM中的byte[]是带编码的，比如，Big5，GBK，GB2312，UTF-8之类的。一个GBK编码的byte[] 转换成 String，

其实就是从GBK编码向Unicode编码转换。一个String转换成一个Big5编码的byte[]，其实就是从Unicode编码向Big5编码转换。所以，Unicode是所有编码转换的中间介质。所有的编码都有一个转换器可以转换到Unicode，而Unicode也可以转换到其他所有的编码

假如我们java本身的环境是utf-8编码，传入的字符串是使用`ISO-8859-1`编码读取的，那么我们获得的String 就是对byte[]类型的字节进行`ISO-8859-1`编码获得的。要想得到其正确显示，可以通过String中的编码功能实现，通常实现的方法为 `String decodedSTring = new String(encodedString.getByte(),"utf-8");`将其通过码表变成byte数组，并通过utf-8编码方式重新解析实现重新编码。

-----------------------

而在io流里面使用字符流读取文件，难免会遇到所用的字符流与文件的编码不一的问题，此时可以通过使用`isr = new InputStreamReader(new fileinputstream(filename),"gbk");`解决。inputstream是字节流，每次读取的是`byte`类型数据，与编码无关，而用InputStreamReader去读取获得的fileinputstream时，可以通过定义编码类型而使用码表映射出原文内容。

## i/o系统类图

![类图](https://github.com/Markkkkks/Photoes/blob/master/io.jpg)