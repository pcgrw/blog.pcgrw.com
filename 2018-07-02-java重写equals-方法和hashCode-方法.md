---

title: java重写equals()方法和hashCode()方法

categories:

- Java

tags:

- Java

abbrlink: a714166d

date: 2018-07-02 12:50:31

---

#### 1.equals()方法和hashCode()方法是什么？ ####

1. equals()和hashCode()都是是Java中万物之源Object类中的方法；
2. equals方法用于比较两个对象是否相同，Object类中equals方法的实现是比较引用地址来判断的对象是否是同一个对象,通过覆盖该方法可以实现自定义的判断规则；
3. hashCode是jdk根据对象的地址或者字符串或者数字计算该对象的哈希码值的方法。

<!-- more -->

#### 2.为什么要重写equals()方法？ ####

1. Object类中equals方法比较的是两个对象的引用地址，只有对象的引用地址指向同一个地址时，才认为这两个地址是相等的，否则这两个对象就不想等。  
2. 如果有两个对象，他们的属性是相同的，但是地址不同，这样使用equals()比较得出的结果是不相等的，而我们需要的是这两个对象相等，因此默认的equals()方法是不符合我们的要求的，这个时候我们就需要对equals()方法进行重写以满足我们的预期结果。  
3. 在java的集合框架中需要用到equals()方法进行查找对象，如果集合中存放的是自定义类型，并且没有重写equals()方法，则会调用Object父类中的equals()方法按照地址比较，往往会出现错误的结果，此时我们应该根据业务需求重写equals()方法。

#### 3.为什么要重写hashCode()方法？ ####

1. hashCode()方法用于散列数据的快速存储，HashSet/HashMap/Hashtable类存储数据时都是根据存储对象的hashcode值来进行分类存储的，一般先根据hashcode值在集合中进行分类，在根据equals()方法判断对象是否相同。  
2. HashMap对象是根据其Key的hashCode来获取对应的Value。  
3. 生成一个好的hashCode值能提高HashSet查找的性能,差的hashCode值不但不能提高性能,甚至可能造成错误。比如hashCode方法中返回常量,会让,HashSet的查找效率退化为List集合的查找效率;hashCode方法中返回随机数,会让查找结果变的不可预测。
4. 好的hashCode生成方式是让对象中的关键属性与质数相乘,并将积相加获取。

#### 4.为什么java中在重写equals()方法后必须对hashCode()方法进行重写？ ####

1. 为了维护hashCode()方法的equals协定，该协定指出：如果根据 equals()方法，两个对象是相等的，那么对这两个对象中的每个对象调用 hashCode方法都必须生成相同的整数结果；而两个hashCode()返回的结果相等，两个对象的equals()方法不一定相等。  
2. HashMap对象是根据其Key的hashCode来获取对应的Value。    
3. 在重写父类的equals()方法时，也重写hashcode()方法，使相等的两个对象获取的HashCode值也相等，这样当此对象做Map类中的Key时，两个equals为true的对象其获取的value都是同一个，比较符合实际。

#### 5.重写equals()方法： ####

重写equals方法需要遵循Java如下规则,否则编码行为会难以揣测:  

1. 自反性：对于任意的对象x，x.equals(x)返回true(自己一定等于自己)；
2. 对称性：对于任意的对象x和y，若x.equals(y)为true，则y.equals(x)亦为true；
3. 传递性：对于任意的对象x、y和z，若x.equals(y)为true且y.equals(z)也为true，则x.equals(z)亦为true；
4. 一致性：对于任意的对象x和y，x.equals(y)的第一次调用为true，那么x.equals(y)的第二次、第三次、第n次调用也均为true，前提条件是没有修改x也没有修改y；
5. 对于非空引用x，x.equals(null)永远返回为false。

重写代码如下：

```java
@Override
public boolean equals(Object o) {
    //自反性
    if (this == o) return true;
    //任何对象不等于null，比较是否为同一类型
    if (!(o instanceof Person)) return false;
    //强制类型转换
    Person person = (Person) o;
    //比较属性值
    return getId() == person.getId() &&
            Objects.equals(getName(), person.getName()) &&
            Objects.equals(getSex(), person.getSex());
}
```

#### 6.重写hashCode()方法： ####

HashMap对象是根据其Key的hashCode来获取对应的Value。  
在重写父类的equals方法时，也重写hashcode方法，使相等的两个对象获取的HashCode也相等，这样当此对象做Map类中的Key时，两个equals为true的对象其获取的value都是同一个，比较符合实际。

重写hashCode()方法需要遵循hashCode()协定：

1. 一致性：在Java应用程序执行期间，在对同一对象多次调用hashCode方法时，必须一致地返回相同的整数，前提是将对象进行hashcode比较时所用的信息没有被修改。
2. equals：如果根据equals()方法比较，两个对象是相等的，那么对这两个对象中的每个对象调用hashCode()方法都必须生成相同的整数结果，注：这里说的equals()方法是指Object类中未被子类重写过的equals()方法。
3. 附加：如果根据equals()方法比较，两个对象不相等，那么对这两个对象中的任一对象上调用hashCode方法不一定生成不同的整数结果。但是，程序员应该意识到，为不相等的对象生成不同整数结果可以提高哈希表的性能。

重写代码如下：

```java
public int hashCode() {
    return Objects.hash(getId(), getName(), getSex());
}
```

[查看源代码](https://github.com/pcstartop/thinkinginjava/blob/master/wikicoding/src/main/java/com/panchao/thinkinginjava/wikicoding/override/Person.java)