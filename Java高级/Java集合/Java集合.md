

# 一、集合分类

## 1、集合大类

- 集合分为<font color="red">**单列集合**</font>和<font color="red">**双列集合**</font>

  - <font color="red">**单列集合**</font>：Collection，即添加数据的时候一次添加一个元素

  ![单列集合](图片/体系结构/单列集合.png)

  - <font color="red">**双列集合**</font>：Map

![双列集合](图片/体系结构/双列集合.png)



## 2、单列集合

![单列集合实现](图片/体系结构/单列集合实现.png)

- List系列集合：添加的元素是有序（存取顺序）、可重复、有索引
- Set系列集合：添加的元素是无序（存取顺序）、不可重复、无索引



### 2.1 Collection

#### 2.1.1 通用方法

- **Collection 是单列集合的祖宗接口，它的功能是全部单列集合都可以继承使用的。**

|              方法名称               |               说明                |
| :---------------------------------: | :-------------------------------: |
|       public boolean add(E e)       |   把给定的对象添加到当前集合中    |
|         public void clear()         |       清空集合中所有的元素        |
|     public boolean remove(E e)      |   把给定的对象在当前集合中删除    |
| public boolean contains(Object obj) | 判断当前集合中是否包含给定的对象  |
|      public boolean isEmpty()       |       判断当前集合是否为空        |
|          public int size()          | 返回集合中元素的个数 / 集合的长度 |

- 例子

~~~java
package org.example.collectiondemo;

import java.util.ArrayList;
import java.util.Collection;

public class CollectionDemo1 {
    public static void main(String[] args) {
        /**
         * public boolean add(E e)        添加
         * public void clear()            清空
         * public boolean remove(E e)     删除
         * public boolean contains(Object obj) 判断是否包含
         * public boolean isEmpty()       判断是否为空
         * public int size()              集合长度
         * <p>
         * 注意点:
         * Collection是一个接口,我们不能直接创建他的对象。
         * 所以，现在我们学习他的方法时，只能创建他实现类的对象。
         * 实现类: ArrayList
         * <p>
         * 目的: 为了学习Collection接口里面的方法
         * 自己在做一些练习的时候, 还是按照之前的方式去创建对象。
         */
        Collection<String> coll = new ArrayList<>();

        /**
         * 1、添加
         * 细节1：如果我们要往List系列集合中添加数据，那么方法永远返回true，因为List系列的是允许元素重复的。
         * 细节2：如果我们要往Set系列集合中添加数据，如果当前要添加元素不存在，方法返回true，表示添加成功。
         * 如果当前要添加的元素已经存在，方法返回false，表示添加失败。
         * 因为Set系列的集合不允许重复。
         */
        coll.add("aaa");
        coll.add("bbb");
        coll.add("ccc");
        System.out.println(coll);

        /**
         * 2.清空
         */
        coll.clear();

        /**
         * 3.删除
         * 细节1：因为Collection里面定义的是共性的方法，所以此时不能通过索引进行删除。只能通过元素的对象进行删除。
         * 细节2：方法会有一个布尔类型的返回值，删除成功返回true，删除失败返回false
         * 如果要删除的元素不存在，就会删除失败。
         */
        System.out.println(coll.remove("aaa"));
        System.out.println(coll);

        /**
         * 4.判断元素是否包含
         * 细节：底层是依赖equals方法进行判断是否存在的。
         * 所以，如果集合中存储的是自定义对象，也想通过contains方法来判断是否包含，那么在javabean类中，一定要重写equals方法。
         */
        boolean result = coll.contains("bbb");
        System.out.println(result);


        /**
         * 5.判断集合是否为空
         */

        boolean result2 = coll.isEmpty();
        System.out.println(result2);//false

        /**
         * 6.获取集合的长度
         */
        int size = coll.size();
        System.out.println(size);//2
    }
}
~~~

- add：添加

   * 细节1：如果我们要往List系列集合中添加数据，那么方法永远返回true，因为List系列的是允许元素重复的。
   * 细节2：如果我们要往Set系列集合中添加数据，如果当前要添加元素不存在，方法返回true，表示添加成功。
   * 如果当前要添加的元素已经存在，方法返回false，表示添加失败。
   * 因为Set系列的集合不允许重复。

- clear：清空

- remove：删除

  - 细节1：因为Collection里面定义的是共性的方法，所以此时不能通过索引进行删除。只能通过元素的对象进行删除。
  - 细节2：方法会有一个布尔类型的返回值，删除成功返回true，删除失败返回false
  - 如果要删除的元素不存在，就会删除失败。

- contains：判断元素是否包含

  - 下面是源码

  ~~~java
  public boolean contains(Object o) {
      return indexOf(o) >= 0;
  }
  
  
  /**
   * 1、首先判断入参o是否为空，如果为空，则遍历集合，只要里面有等于null的就返回
   * 2、如果不为空，则用equals进行遍历判断，返回对应的对象
   */
  public int indexOf(Object o) {
      if (o == null) {
          for (int i = 0; i < size; i++)
              if (elementData[i]==null)
                  return i;
      } else {
          for (int i = 0; i < size; i++)
              if (o.equals(elementData[i]))
                  return i;
      }
      return -1;
  }
  ~~~

  - 细节：<font color="red">**底层是依赖equals方法进行判断是否存在的。**</font>
  - 所以，如果集合中存储的是自定义对象，也想通过contains方法来判断是否包含，那么在javabean类中，一定要重写equals方法。
  - 如果存的是自定义对象，没有重写equals方法，那么默认使用Object类中的equals方法进行判断，而Object类中equals方法，依赖地址值进行判断，那么就无法匹配上。

  ~~~java
  package org.example.collectiondemo;
  
  public class Student {
      private String name;
      private int age;
  
      public Student(String name, int age) {
          this.name = name;
          this.age = age;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public int getAge() {
          return age;
      }
  
      public void setAge(int age) {
          this.age = age;
      }
      
      @Override
      public boolean equals(Object o) {
          if (o == null || getClass() != o.getClass()) return false;
          Student student = (Student) o;
          return age == student.age && Objects.equals(name, student.name);
      }
  }
  
  
  package org.example.collectiondemo;
  
  import java.util.ArrayList;
  import java.util.Collection;
  
  public class CollectionDemo2 {
      public static void main(String[] args) {
          Collection<Student> collection = new ArrayList<>();
          collection.add(new Student("张三", 18));
          collection.add(new Student("李四", 18));
  		// 需求：如果姓名和年龄一致就认为是同一个对象包含
          // 当Student没有重写equals方法时，打印 false
          // 当Student重写equals方法时，打印 true
          System.out.println(collection.contains(new Student("李四", 18)));
      }
  }
  ~~~

- isEmpty：判断集合是否为空

- size：获取集合的长度



#### 2.1.2 遍历方式

- 迭代器遍历
- 增强for遍历
- Lambda表达式遍历



##### 2.1.2.1 迭代器遍历

- 迭代器在 Java 中的类是**Iterator**，迭代器是集合专用的遍历方式。
- <font color="red">**Collection 集合获取迭代器**</font>
  - Iterator<E> iterator()：返回迭代器对象，默认指向当前集合的 0 索引
- <font color="red">**Iterator 中的常用方法**</font>
  - boolean hasNext()：判断当前位置是否有元素，有元素返回 true，没有元素返回 false
  - E next()：获取当前位置的元素，并将迭代器对象移向下一个位置。

- <font color="red">**迭代器遍历不依赖索引**</font>

![迭代器遍历](图片/体系结构/迭代器遍历.png)

~~~java
package org.example.collectiondemo;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

public class CollectionDemo3 {
    public static void main(String[] args) {
        // 1.创建集合并添加元素
        Collection<String> coll = new ArrayList<>();
        coll.add("aaa");
        coll.add("bbb");
        coll.add("ccc");
        coll.add("ddd");

        // 2.获取迭代器对象
        // 迭代器就好比是一个箭头，默认指向集合的0索引处
        Iterator<String> it = coll.iterator();
        // 3.利用循环不断的去获取集合中的每一个元素
        while(it.hasNext()){
            // 4.next方法的两件事情：获取元素并移动指针
            String str = it.next();
            System.out.println(str);
        }
    }
}

/**
 aaa
 bbb
 ccc
 ddd
 */
~~~

- 细节注意点
  - 当指针已经指向最后没有元素的位置，再强行使用next()方法，就会报错 NoSuchElementException
  - 迭代器遍历完毕，指针不会复位，想再重新遍历，只能重新再次获取一个新的迭代器对象
  - 循环中只能用一次 next 方法：
    - 因为next()方法是有两步的，分别是 获取值 和 移动指针
    - 上一个指针已经移动到最后了，第二个再移动就会报错 NoSuchElementException
  - 迭代器遍历时，不能用集合的方法进行增加或者删除
    - 会报错并发修改异常：java.util.ConcurrentModificationException
    - 如果是倒数第二个，则不会报错原因：异常还没爆出就结束了
    - 如果实在要删除，那么可以用迭代器提供的remove方法进行删除

~~~java
package org.example.collectiondemo;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

public class CollectionDemo3 {
    public static void main(String[] args) {
        Collection<String> coll = new ArrayList<>();
        coll.add("aaa");
        coll.add("bbb");
        coll.add("ccc");
        coll.add("ddd");

        // 这里已经将指针移动到最后了
//        Iterator<String> it = coll.iterator();
//        while (it.hasNext()) {
//            String str = it.next();
//            System.out.println(str);
//        }

        /**
         * 1、当指针已经指向最后没有元素的位置，再强行使用next()方法，就会报错 NoSuchElementException
         */
//        String str = it.next();

        /**
         * 2、迭代器遍历完毕，指针不会复位，想再重新遍历，只能重新再次获取一个新的迭代器对象
         */
//        Iterator<String> iterator = coll.iterator();
//        while(iterator.hasNext()){
//            String s = iterator.next();
//            System.out.println(s);
//        }

        /**
         * 3、循环中只能用一次 next 方法
         * 因为next()方法是有两步的，分别是 获取值 和 移动指针
         * 上一个指针已经移动到最后了，第二个再移动就会报错 NoSuchElementException
         */
//        Iterator<String> it = coll.iterator();
//        while(it.hasNext()){
//            System.out.println(it.next());
//            System.out.println(it.next());
//        }

        /**
         * 4、不能用集合的方法进行增加或者删除
         * 
         */
        Iterator<String> it = coll.iterator();
        while(it.hasNext()){
           String str = it.next();
           if ("bbb".equals(str)) {
               // 会报错java.util.ConcurrentModificationException，并发操作异常
//               coll.remove(str);
               // 实在要删除，就用迭代器的remove
               it.remove();
           }
        }
        System.out.println(coll);
    }
}
~~~



##### 2.1.2.2 增强for遍历

- 增强 for 的底层就是迭代器，为了简化迭代器的代码书写的。
- 它是 JDK5 之后出现的，其内部原理就是一个Iterator迭代器。
- 所有的单列集合和数组才能用增强 for 进行遍历。
- 格式

~~~java
for (数据类型 变量名 : 集合/数组) {
    
}
~~~

- s其实就是一个第三方变量，在循环的过程中依次表示集合中的每一个数据，<font color="red">**修改其值不会改变集合中原本的数据**</font>

~~~java
package org.example.collectiondemo;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

public class CollectionDemo4 {
    public static void main(String[] args) {
        //1.创建集合并添加元素
        Collection<String> coll = new ArrayList<>();
        coll.add("zhangsan");
        coll.add("lisi");
        coll.add("wangwu");

        // 2.利用增强for进行遍历
        // 注意点：
        // s其实就是一个第三方变量，在循环的过程中依次表示集合中的每一个数据，修改其值不会改变集合中原本的数据
        for(String s : coll){
            s = "qqq";
        }

        System.out.println(coll);  //[zhangsan, lisi, wangwu]
    }
}
~~~



##### 2.1.2.3 lambda表达式遍历

- 得益于 JDK 8 开始的新技术 Lambda 表达式，提供了一种更简单、更直接的遍历集合的方式。
- 方法：default void forEach(Consumer<? super T> action)  :  结合 lambda 遍历集合

~~~java
package org.example.collectiondemo;

import java.util.ArrayList;
import java.util.Collection;
import java.util.function.Consumer;

public class CollectionDemo5 {
    public static void main(String[] args) {
        // 1.创建集合并添加元素
        Collection<String> coll = new ArrayList<>();
        coll.add("zhangsan");
        coll.add("lisi");
        coll.add("wangwu");

        // 2.利用匿名内部类的形式
        // 底层原理：
        // 其实也会自己遍历集合，依次得到每一个元素
        // 把得到的每一个元素，传递给下面的accept方法
        // s依次表示集合中的每一个数据
        /*
        coll.forEach(new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        });
        */
        
        //lambda表达式
        coll.forEach(s -> {
            System.out.println(s);
        });
    }
}
~~~

- forEach底层源码

~~~java
default void forEach(Consumer<? super T> action) {
    Objects.requireNonNull(action);
    // 就是这里的for循环遍历，然后把每个对象交给匿名内部类的accept方法，所以在外部使用时，只需要重写accept方法即可操作每个元素
    for (T t : this) {
        action.accept(t);
    }
}
~~~



### 2.2 List

#### 2.2.1 通用方法

- 有序：存和取的元素顺序一致（存取）
- 有索引：可以通过索引操作元素
- 可重复：存储的元素可以重复

- Collection 的方法 List 都继承了，可以直接用

- List 集合因为有索引，所以多了很多索引操作的方法。

| 方法名称                      | 说明                                   |
| ----------------------------- | -------------------------------------- |
| void add(int index,E element) | 在此集合中的指定位置插入指定的元素     |
| E remove(int index)           | 删除指定索引处的元素，返回被删除的元素 |
| E set(int index,E element)    | 修改指定索引处的元素，返回被修改的元素 |
| E get(int index)              | 返回指定索引处的元素                   |

~~~java
package org.example.listdemo;

import java.util.ArrayList;
import java.util.List;

public class ListDemo1 {
    public static void main(String[] args) {
        /**
         * List集合独有的方法:

         * void add(int index,E element)      在此集合中的指定位置插入指定的元素
         * E remove(int index)                删除指定索引处的元素，返回被删除的元素
         * E set(int index,E element)         修改指定索引处的元素，返回被修改的元素
         * E get(int index)                   返回指定索引处的元素
         */

        // 1.创建一个集合
        List<String> list = new ArrayList<>();

        // 2.添加元素
        list.add("aaa");
        list.add("bbb");//1
        list.add("ccc");

        // void add(int index,E element)      在此集合中的指定位置插入指定的元素
        // 细节：原来索引上的元素会依次往后移
        list.add(1, "QQQ");

        // E remove(int index)                删除指定索引处的元素，返回被删除的元素
        String remove = list.remove(0);
        System.out.println(remove);//aaa

        // E set(int index,E element)           修改指定索引处的元素，返回被修改的元素
        String result = list.set(0, "QQQ");
        System.out.println(result);
        
        // E get(int index)                     返回指定索引处的元素
        String s = list.get(0);
        System.out.println(s);

        //3.打印集合
        System.out.println(list);
    }
}
~~~

- add(int index,E element) ：在此集合中的指定位置插入指定的元素

  - 原来索引上的元素会依次往后移

- E remove(int index) ：删除指定索引处的元素，返回被删除的元素

  - 小细节，list.remove(1);除的是索引下标1的数据，而不是值为1的数据
  - 原因：<font color="red">**优先调用，实参跟形参类型一致的那个方法。**</font>这里有俩方法，肯定优先使用索引下标的方法

  ![list_remove.png](图片/体系结构/list_remove.png)

  - 如果想删除值为1的，就需要将其手动装箱，变成Integer类型，那么就会自动调用删除对象的方法
  

~~~java
package org.example.listdemo;

import java.util.ArrayList;
import java.util.List;

public class ListDemo2 {
    public static void main(String[] args) {
        // 1.创建集合并添加元素
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);

        // 2.删除元素
        // 请问：此时删除的是1这个元素，还是1索引上的元素？
        // 为什么？
        // 因为在调用方法的时候，如果方法出现了重载现象
        // 优先调用，实参跟形参类型一致的那个方法。
        // 所以这里删除的是索引下标1的数据，而不是值为1的数据
        list.remove(1);

        // 手动装箱，手动把基本数据类型的1，变成Integer类型
        Integer i = Integer.valueOf(1);
        list.remove(i);

        System.out.println(list);
    }
}
~~~

- E set(int index,E element) ：修改指定索引处的元素，返回被修改的元素
-  E get(int index)： 返回指定索引处的元素



#### 2.2.2 遍历方式

- 迭代器遍历
- 列表迭代器遍历
- 增强for遍历
- lambda表达式遍历
- 普通for遍历

![list遍历对比.png](图片/体系结构/list遍历对比.png)

~~~java
package org.example.listdemo;

import java.util.ArrayList;
import java.util.List;
import java.util.ListIterator;
import java.util.function.Consumer;

public class ListDemo3 {
    public static void main(String[] args) {
        //创建集合并添加元素
        List<String> list = new ArrayList<>();
        list.add("aaa");
        list.add("bbb");
        list.add("ccc");

        //1.迭代器
        /*Iterator<String> it = list.iterator();
        while(it.hasNext()){
            String str = it.next();
            System.out.println(str);
        }*/

        //2.增强for
        //下面的变量s，其实就是一个第三方的变量而已。
        //在循环的过程中，依次表示集合中的每一个元素
        /* for (String s : list) {
            System.out.println(s);
        }*/

        //3.Lambda表达式
        list.forEach(new Consumer<String>() {
            @Override
            public void accept(String s) {

            }
        });

        //4.普通for循环
        //size方法跟get方法还有循环结合的方式，利用索引获取到集合中的每一个元素
        /*for (int i = 0; i < list.size(); i++) {
            //i:依次表示集合中的每一个索引
            String s = list.get(i);
            System.out.println(s);
        }*/

        // 5.列表迭代器
        //获取一个列表迭代器的对象，里面的指针默认也是指向0索引的

        //额外添加了一个方法：在遍历的过程中，可以添加元素
        ListIterator<String> it = list.listIterator();
        while(it.hasNext()){
            String str = it.next();
            if("bbb".equals(str)){
                //qqq
                it.add("qqq");
            }
        }
    }
}
~~~

- 列表迭代器：
  - <font color="red">**void add(E e)**</font>：将指定的元素插入列表（可选操作）。
  - <font color="red">**boolean hasNext()**</font>：以正向遍历列表时，如果列表迭代器有多个元素，则返回 true（换句话说，如果 `next` 返回一个元素而不是抛出异常，则返回 true）。
  - boolean hasPrevious()：如果以逆向遍历列表，列表迭代器有多个元素，则返回 true。
  - <font color="red">**E next()**</font>：返回列表中的下一个元素。
  - int nextIndex()：返回对 next 的后续调用所返回元素的索引。
  - E previous()：返回列表中的前一个元素。
  - int previousIndex()：返回对 previous 的后续调用所返回元素的索引。
  - <font color="red">**void remove()**</font>：从列表中移除由 next 或 previous 返回的最后一个元素（可选操作）。
  - void set(E e)：用指定元素替换 next 或 previous 返回的最后一个元素（可选操作）。



2.3 