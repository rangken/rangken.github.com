---
layout: post
title:  "Java Generic"
date:   2015-09-19 00:00:00 +0900
categories: JAVA
tags: JAVA
description: Java Generic Wildcard 자료
---

### Java Generic
- 자바 제네릭을 많이 사용하지만 매번 햇갈리고 재대로 이해했는지 모를때가 많아서 햇갈리는 부분만 조금 정리해 보기 :cry:
사실은 그냥 블로그 너무 오래안해서..... :yum:
- 제네릭은 용어부터 햇갈려서 간단히 정리([이펙자바](../effective-java-4))
	- 간단히 말하면 
		- \<E\>(`Gereric`) vs \<?\>(`Wildcard`)
		- \<E\>(`unbounded`) vs \<E extends \>(`bounded`)
	- 형인자(type parameter)
	- 형인자 자료형(parameterized type) : **List \<String\>**
	- 실형인자(actual type parameter) : **String**
	- 제네릭 자료형(generic type) : **List\<E\>**
	- 형식 형인자(Formal Type Parameter) : **E**
	- 무인자 자료형(raw type) : **List**
	- 한정적 형인자(bunded type parameter) : **\<E extends Number\>**
	- 재귀적 형 한정(recursive type bound) : **\<T extends Comparable\<T\>>**
	- 비한정적 와일드카드 자료형(unbounded wildcard type) : **List\<?\>**
	- 한정적 와일드카드 자료형(bounded wildcard type) : **List<? extends Number>**
	- 제네릭 메서드(generic method) : **static <E> List<E> asList(E[] a)**
	- 자료형 토큰(type token) : **String.class**

### Wildcard
- **자바 Generic은 컴파일 타임에서 형 안정성을 지원하기 위해 존재한다.** 따라서 Generic은 기본적으로 다형성(Polymorphism) 을 갖지 않는다.
- Gereric 에 다형성을 갖도록 만들기 위해 Wildcard 를 사용한다. List\<Animal\> 에는 Dog, Cat 클래스는 add 할수가 없다. 따라서 List\<? extends Animal> 로 선언해서 다형성을 갖을수 있도록 하는것이 Wildcard 의 핵심!
- extends 이외에 super 키워드도 존재한다 :worried:

{% highlight java %}
// ? 가 extends Number 을 한다! (upper bound)
List<? extends Number> foo3 = new ArrayList<Number>();
List<? extends Number> foo3 = new ArrayList<Integer>();
List<? extends Number> foo3 = new ArrayList<Double>();
// ? 는 Integer 에 super 클래스이다. (lower bound)
List<? super Integer> foo3 = new ArrayList<Integer>();
List<? super Integer> foo3 = new ArrayList<Number>();
List<? super Integer> foo3 = new ArrayList<Object>();
{% endhighlight %}

### Unbounded Wildcard \<?\>
- Unbounded Wildcard 은 언제 쓰는것일까? 와일드 카드에 ? 는 결국에 컴파일 시간에는 결국 Object 객체 일뿐이다. 즉 List\<?\> 에 String 객체를 넣을수는 없다(컴파일 에러)
- List\<Object\> 에는 String 객체를 넣을수가 있다. 컴파일 시간에 형 안정성을 보장하지 못하고 런타임에서 에러가 발생 할 수 있다. 
- List\<?\> 는 String 을 `add` 할수는 없지만 `contains(Object o)`, `remove(Object o)` 같이 형인자 를 사용하지 않은 함수들은 그대로 사용 할 수 있다.
- 즉 Unbounded Wildcard 는 컴파일 시간에 형 안정성을 보장하며 형인자에 대한 정보가 없으므로 형인자를 갖는 부분을 제외한 메소드만 사용이 가능하다.
	- List\<String\> 는 비구체화 타입이다. 런타임에서는 타입정보가 사라져서 컴파일 타임에서 런타임에서 보다 더 많은 정보를 갖는 타입이다.
	- List\<?\> 은 구체화 타입이다. 컴파일 타입에서는 타입 정보를 알수 없지만 런타임에서는 더 많은 정보를 갖는다.

### Gerenic Stata Method

{% highlight java %}
public class Test<E> {
	public static <E> Test<E> of(){
		return null;
	}
	public Test<E> of2(){
		return null;
	}
}
{% endhighlight %}
- Static 함수는 사용시점에 E 클래스에 대한 정보가 없으므로 static 옆에 <E> 정보가 있어야 한다.
	- Test.<Object>of 로 사용, _컴파일러가 타입 추론을 해서 사실 위 경우는 해주지 않아도 컴파일 에러가 발생하진 않는다._
- Instance 함수는 사용시점에 이미 인스턴스를 생성해서 클래스에 대한 정보를 가지고 있으므로 <E> 에 대한 정보가 없어도 된다.
	- t.of2() 로 사용

### Generic Casting
- List<Number> 와 List<Integer> 는 상속 관계에 있지 않다.
  - List<Number> list = new List<Integer> 는 불가능하다.
  - List<Number> list; list.add(1) 이런식은 가능한데 Number 로 캐스팅이 되기 때문에 가능한거다.


