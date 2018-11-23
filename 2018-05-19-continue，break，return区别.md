---

title: continue，break，return区别

categories:

- Java

tags:

- Java

abbrlink: 5cd9b8ba

date: 2018-05-19 19:09:42

---

#### 1. continue ####

continue关键字是用来中止本次循环然后开始下一次循环的。

<!-- more -->

```java 
/**
 * continue
 */
public static void testContinue() {
    for (int i = 0; i < 3; i++) {
        System.out.println("i的值是" + i);
        if (i == 1) {
            continue;
        }
        System.out.println("testContinue第"+i+"次结束");
    }
}
```

执行结果如下:
```java
-------------------testContinue测试开始--------------------
i的值是0
testContinue第0次结束
i的值是1
i的值是2
testContinue第2次结束
-------------------testContinue测试结束--------------------
```

#### 2. break ####

break关键字是用来完全结束一个循环，跳出循环体。循环体中一旦遇到break，循环将完全结束，然后运行循环之后的代码。
```java
/**
 * break
 */
public static void testBreak() {
    for (int i = 0; i < 3; i++) {
        System.out.println("i的值是" + i);
        if (i == 1) {
            break;
        }
        System.out.println("testBreak第"+i+"次结束");
    }
}
```

执行结果如下:
```java
-------------------testBreak测试开始--------------------
i的值是0
testBreak第0次结束
i的值是1
-------------------testBreak测试结束--------------------
```

break关键字还可以结束其外层循环。此时需要在break后紧跟一个标签，这个标签用于标识一个外层循环。   

> Java中的标签就是一个紧跟着英文冒号(:)的标识符。且它必须放在循环语句之前才有作用。 

```java
/**
 * breakTag
 */
public static void testBreakTag() {
    tag:
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            System.out.println("i的值为:" + i + " j的值为:" + j);
            if (j == 1) {
                break tag;
            }
        }
    }
}
```

执行结果如下:
```java
-------------------testBreakTag测试开始--------------------
i的值为:0 j的值为:0
i的值为:0 j的值为:1
-------------------testBreakTag测试结束--------------------
```

#### 3. return ####

return关键字的是用来结束一个方法，并不是专门用于跳出循环的。 方法体内一旦运行到return语句处，将会结束该方法，循环自然也就随之结束。
```java
/**
 * return
 */
public static void testReturn() {
    for (int i = 0; i < 3; i++) {
        System.out.println("i的值是" + i);
        if (i == 1) {
            return;
        }
        System.out.println("testReturn第"+i+"次结束");
    }
}
```

执行结果如下:
```java
-------------------testReturn测试开始--------------------
i的值是0
testReturn第0次结束
i的值是1
-------------------testReturn测试结束--------------------
```

[查看源代码](https://github.com/pcstartop/thinkinginjava/blob/master/wikicoding/src/main/java/com/panchao/thinkinginjava/wikicoding/basics/ContinueOrBreakOrReturnDifference.java)
