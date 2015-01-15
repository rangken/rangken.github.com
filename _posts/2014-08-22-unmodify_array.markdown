---
layout: post
title: "Java final array & unmodifiableList"
date: 2014-08-22 15:13:42 +0900
categories:
redirect_from: "{{page.url}}"
description: Java final array & unmodifiableList
---


## Java unmodifiableList
- Java 에서 변경 불가능한 자료구조 
- 단순 final array 로는 내부 데이터 변경이 가능하다.
    - array 자체를 변경 할수 없을뿐

{% highlight java linenos %}
import java.util.*;
class Main{
  public static void main(String args[]){
    String str1 = "hello";
    String str2 = "world";
    String str3 = "jaeyoung";
    final String test[] = { str1, str2, str3 };
    test[2] = "rangken";
    // 변경이 가능하다. test array 가 final 이다
    // test = anotherarr; 는 불가능하다
    // final List<String> 도 마찬가지
    for(String s : test){
      System.out.println(s); // hello world rangken
    }
    // 변경 불가능한 객체를 담는 array
    List<String> items = Collections.unmodifiableList(Arrays.asList("hello","world","jaeyoung"));
    // 런타임 에러 발생
    items.add("rangken");
  }
{% endhighlight %}

- 변경 불가능한 자료구조를 만들기 위해서는 Google Guava 를 사용하면 좋다.
- 계속!
