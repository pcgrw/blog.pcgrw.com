---

title: Comparable和Comparator区别

categories:

- Java

tags:

- Java

abbrlink: 94f434c3

date: 2018-05-22 18:24:06

---

Comparable是一个内比较器,而Comparator是一个外比较器，本文将讨论这两种比较器的区别，以及在开发过程中什么时候应该用Comparable,什么时候又该用Comparator

----------

<!-- more -->

### Comparable ###

Comparable是一个内比较器，很多类都会实现这个接口以提供对该类对象之间比较的默认实现；比如String，Integer,Float,Double类都实现了Comparable接口。实现Comparable接口的类有一个特点，就是这些类提供了自己对象之间比较大小的默认实现，如果开发者add进入一个Collection的对象想要通过Collections的sort方法帮你自动进行排序的话，那么这个add进入的对象类必须实现Comparable接口。  

Comparable接口里面有一个compareTo方法,compareTo方法的返回值是int，有三种情况：

- 比较者(调用compareTo方法者)大于被比较者（也就是compareTo方法接受对象），那么返回 1;  

- 比较者等于被比较者，那么返回0;  

- 比较者小于被比较者，那么返回 -1;  

```java

package com.panchao.ssmtest.util;

public class Person implements Comparable<Person>{
	private String id;
	private String name;
	private String sex;
	public Person(String id, String name, String sex) {
		super();
		this.id = id;
		this.name = name;
		this.sex = sex;
	}
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	@Override
	public String toString() {
		return id;
	}
	@Override
	public int compareTo(Person arg0) {
		if (Integer.parseInt(this.getId()) > Integer.parseInt(arg0.getId())) {
			return 1;
		}else if(Integer.parseInt(this.getId()) < Integer.parseInt(arg0.getId())) {
			return -1;
		}else {
			return 0;
		}
	}
}

```

```java

package com.panchao.ssmtest.util;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Random;

public class Test {
	
	public static String createId() {
		Random random = new Random();
		return String.valueOf(random.nextInt(50));
	}
	
	public static void main(String[] args) {
		List<Person> list = new ArrayList<>();
		for (int i = 0; i < 10; i++) {
			Person p = new Person(createId(), createId(), createId());
			list.add(p);
		}
		Collections.sort(list);
		System.out.println(list);
	}
}

```

### Comparator ###

Comparator是一个外比较器，有两种情况可以使用实现Comparator接口的方式：

- 一个对象不支持自己和自己比较（没有实现Comparable接口），但是又想对两个对象进行比较；一般同类型对象比较很少实现这个接口。  

- 一个对象实现了Comparable接口，但是开发者认为compareTo方法中的比较方式并不是自己想要的那种比较方式。Comparator接口更多是实现这个功能。对特定比较需求提供支持。  

Comparator接口里面有一个compare方法，方法有两个参数T o1和T o2，是泛型的表示方式，分别表示待比较的两个对象，方法返回值和Comparable接口一样是int，有三种情况：

- o1大于o2，返回1;  

- o1等于o2，返回0;  

- o1小于o3，返回-1;  

```java
package com.panchao.ssmtest.util;

public class Person{
	private String id;
	private String name;
	private String sex;
	public Person(String id, String name, String sex) {
		super();
		this.id = id;
		this.name = name;
		this.sex = sex;
	}
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	@Override
	public String toString() {
		return id;
	}
}

```

```java
package com.panchao.ssmtest.util;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;
import java.util.Random;

public class Test {
	
	public static String createId() {
		Random random = new Random();
		return String.valueOf(random.nextInt(50));
	}
	
	public static void main(String[] args) {
		List<Person> list = new ArrayList<>();
		for (int i = 0; i < 10; i++) {
			Person p = new Person(createId(), createId(), createId());
			list.add(p);
		}
		list.sort(new Comparator<Person>() {

			@Override
			public int compare(Person arg0, Person arg1) {
				if (Integer.parseInt(arg0.getId()) > Integer.parseInt(arg1.getId())) {
					return 1;
				}else if(Integer.parseInt(arg0.getId()) < Integer.parseInt(arg1.getId())) {
					return -1;
				}else {
					return 0;
				}
			}});
		System.out.println(list);
	}
}

```

----------

[源代码地址](https://github.com/pcstartop/thinkinginjava/tree/master/wikicoding/src/main/java/com/panchao/thinkinginjava/wikicoding/comparer)
