# 容器

## 容器的结构

![test](https://raw.githubusercontent.com/Markkkkks/Photoes/master/Collections.png)

容器主要分为Collection和Map。Collection中主要包括List Set 和 Queue；Map时一组成对的"键值对"对象，允许使用键来查找值。

## 添加元素的方法

```shell
添加元素主要使用的方法
Collections.addAll(Collection,array[]);
collection.addAll(collection);//collection是实现了Collection接口的对象
Array.asList();//返回一个List形式的数组
```

## List

### 包含的集合

- ArrayList 擅长与随机访问元素，插入移除元素较慢
- LinkedList 随机访问比较慢，但是插入删除快

### 特性：

- List允许它被创建之后添加元素，移除元素，或者自我调整尺寸。
- List的remove();indexOf();contain();retainAll();方法与传入对象的equals()方法相关

常用方法：

```shell
subList();返回子序列 父序列中调用containAll();会返回true
有趣的是 子序列的顺序与 containAll();返回结果无关，只要子序列中包含的元素在父序列中都有 一定返回true;
```

### 迭代器

#### iterator

作用：迭代器是一个对象，工作时遍历并且选择序列中的对象，

```java
//简单的使用迭代器
iterator it = List.iterator();
while (it.hasNext())
{
    Object item = it.next();
    //do something
}
```

#### ListIterator

作用：更加强大的Iterator子类型，只能用于各种List类的访问，支持双向移动

```java
//双向iterator
ListIterator lit = List.listIterator();
while (it.hasNext())
{
    Object item = it.next();
    //do something
}
while(it.hasPrevious())
{
    Object item = it.next();
    //do something
}
lit = list.listIterator(3);
```

### LinkedList

实现了基本List借口，但可以使其使用作栈，队列或者双端队列更加方便

常用方法

```java
Linklist ll = new Linklist();
ll.getFirset();
ll.element();
ll.peek();//以上都取第一个元素，但不更改
ll.remove();
ll.poll();
ll.removeFirst();//以上都取并删除的一个元素
//---------------
ll.offer();
ll.add();
ll.addLast();//都是加在末尾
```

## Set

Set不保存重复的元素(注意equals和hashcode方法);会阻止将相同对象的多个实例添加到Set中

包含的collection

- HashSet 元素储存通过散列函数
- LinkedHashSet 元素储存通过红黑树数据结构储存
- TreeSet 根据排序器的排序方法，从构造函数或者add函数中有顺序的添加元素

## Map

Map可以很容易的扩展到多维，如`Map<Person,List<Pet>>`

Map可以返回它的键的Set(KeySet())，它的值的Collection(values())或者它的键值对的Set(Set<Map.Entry<K,V>> entrySet())。

## Queue

所有的队列都有以下方法

- offer();插入尾元素
- peek() element();获取不删除头元素 peek()返回空 element()返回前者返回空，后者返回NoSuchElementException
- poll() remove();获取第一个元素并删除 前者返回空，后者返回NoSuchElementException

ps：Linkedlist实现了Queue接口，因此可以直接转型为Queue

### PriorityQueue

优先级队列，在队列中会自动根据优先级排序(优先级以小为高)
当在PriorityQueue上调用offer()方法插入对象时，这个对象会在队列中被排序。默认的排序将使用对象在队列中的自然顺序(根据compareTo函数返回的结果)。
eg:

```java
PriorityQueue priorityQueue = new PriorityQueue<Integer>();
priorityQueue.offer(5...8...2...4...3);
printQueue();//打印结果为 2 3 4 5 8
int []ints = new int[]{1,2,3,4,5};
priorityQueue = new PriorityQueue(ints.size(),Collections.reverseOrder());
priorityQueue.addAll(iints);
printQueue();//打印结果为5 4 3 2 1
```

## Collection 与 Iterator

查看文档发现Coleection接口的父接口是Iterable，Iterable中包含方法iterator()，因此要实现自己的Collection，必须实现其iterater()方法，其中iterator()方法返回的Iterator类中需要重载方法hasNext();next();remove();

如果需要实现一个自定义的Collection，可以选择`implements Collection`接口(必须实现接口所有方法)或者`extends AbstractCollection` *(只需要实现iterator()和size()方法即可)* 以下是demo

```java
class  MyCollection extends  AbstractCollection<Info>{
 private Info []arr =new Info[100];
 private int count= 0;
 public  boolean add(Info info){
  if(count<=arr.length){
      arr[count++]=info ;
   return true ;
  }
  return false;
 }@Override
 public boolean equals(Object obj) {
  // TODO Auto-generated method stub
  return super.equals(obj);
 }
 @Override
 public Iterator<Info> iterator() {
  return new Iterator<Info>() {
   private int flag=0 ;
   @Override
   public boolean hasNext() {
    return flag<MyCollection.this.count ;
   }
   @Override
   public Info next() {
    return arr[flag++];
   }
   @Override
   public void remove() {   //支持删除操作
          throw new  UnsupportedOperationException() ;
   }
  };
 }
 @Override
 public int size() {
  return 0;
 }
}
```


## foreach 与iterator

所有的`collection`都可以实现 `foreach`方法，之所以是这样，原因是java SE5引入了Iterable接口，该接口包含能产生`Iterator`的`iterator()`方法，并且`Iterable`接口被`foreach`用来在序列中移动，如果自己创建任何实现`Iterable`的类，都可以将它用在`foreach`语句中,但相反的,尝试把数组或其他未实现Iterable接口的类进行`foreach`,会导致失败。(但莫名其妙地在java1.7版本运行数组的foreach是成功的)

### 适配器方法惯用法

添加一种或者多种在`foreach`语句中使用这个类的方法，应该通过改写该类的返回iterator的方法 例子如下

```java
class ReversibleArrayList<T> extends ArrayList<T>{
    public ReversibleArrayList(Collection<T>c ){super(c);}
    public Iterable<T> reversed(){
        return new Iterable<T>(){
            int current = size() - 1;
            public boolean hasNext(){return current>-1;}
            public T next(){return get(current--);}
            public void remove(){throw new UnsupportedOperationException();}
        }
    }
}
public class AdapterMethodIdiom{
    public static void main(String [] args){
        ReversibleArrayList<String>ral = new ReversibleArrayList<String>(Arrays.asList("To be or not to be".split(" ")));
        for(String s : ral)
            System.out.print(s+ " ");
        for(String s : ral.reversed())
            System.out.print(s+ " ");
    }
}
```
