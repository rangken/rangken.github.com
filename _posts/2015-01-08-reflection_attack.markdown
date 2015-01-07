---
layout: post
title:  "Java Reflection 객체 생성"
date:   2015-01-08 00:30:40 +0900
categories: Java
tags: Java
description: Reflection Attack
---


### Reflection Attack
- 생성자를 private 로 선언해서 외부 생성을 막으려고 해도 reflection 을 사용하면 외부에서 생성 가능하도록 할수 있다.

{% highlight java linenos %}
import java.lang.reflect.*;

class Test{
    // private 이므로 외부 생성 불가
    private Test()
    {
      System.out.println("Test");
    }
}
class Main{

  public static void main(String[] args) throws ClassNotFoundException,
         InstantiationException, IllegalAccessException, ReflectiveOperationException{
    Class<Test> kclass = Test.class;

    Constructor<Test> cons = kclass.getDeclaredConstructor(); // 생성자를 가져옴
    cons.setAccessible(true); // 생성자를 public 으로 변경

    Object object = cons.newInstance(); // 생성 가능

  }
}
{% endhighlight %}
- 리플랙션을 통해서 객체는 언제든 생성 가능하다
  - 기본 생성자가 없더라도 생성 가능
  - private 로 선언하더라도 setAccessible 을 사용해 변경후 생성 가능

