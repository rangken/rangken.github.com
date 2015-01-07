---
layout: post
title:  "Effective Java 3장 모든 객체에 공통적인 메소드"
date:   2014-08-26 18:07:40 +0900
categories: Java
tags: Java effective-java
description: Modifying latitude and longitude with a distance.
---

## Effective-Java 모든 객체에 공통적인 메소드 (8~12)

### ITEM8 : equals 메소드를 오버라이딩 할때는 보편적 계약을 따르자.
- 메소드를 오버라이딩 하지 않는게 되도록 좋다. 특별한경우 가 아니라면!
     - 클래스의 각 인스턴스가 본래부터 유일한 경우 : Thread 와 같은 클래스 처럼 값의 논리적인 비교는 의미가 없으며 객체 참조가 같으면(==) 동일한 것임을 알 수 있기 때문에
     - 두 인스턴스가 논리적으로 같은지 검사하지 않아도 되는 클래스의 경우 : java.util.Random 과 같은 클래스, equals 같은 기능이 필요가없다.
     - 수퍼 클래스에서 equals 메소드를 이미 오버라이딩 했고 그 메소드를 그대로 사용해도 좋은경우 : Set 의 AbstractSet, List 의 AbstractList
     - private 이거나 package 전용 클래스라서 이클래스의 equals 메소드가 절대 호출되지 않아야 할 경우.

{% highlight java linenos %}
@Override public boolean equals(Object o){
    throw new AssertionError(); // 에러를 발생 시켜준다.
}
{% endhighlight %}

- 특별한 경우 오버라이딩 되도록 해도 좋다. Value 클래스 같은경우 하나의 값을 나타내는 클래스로써 Integer Date와 같은것들이다. 같은 객체 참조 여부는 중요하지 않고 객체가 갖는 값이 논리적으로 같은지가 관심있는 클래스의 경우다. *Map 이나 Set 의 요소로 사용될경우 equals 메소드의 오버라이딩이 꼭 필요하다. 같은 값의 객체가 이미 있는지 비교하는 수단을 제공해야 되기 떄문이다.*

- 성질
  - 재귀성 : null 아닌 x.equals(x) 반드시 true 여야한다.
  - 대칭성 : null 아닌 x.quals(y) 이면 반대로 true다!
  - 이행성 : x.equals(y) y.equals(z) 이면 x.equals(z) 여야한다.
  - 일관성 : 한번 같으면 계속 같아야한다.

- 상속을 사용할경우 완벽한 equals 를 구현하기 힘들다. 차라리 Compiosition( 변수로 선언 ) 을 하는것이 좋다.
     - java.sql.Timestamp 는 java.util.Date 로 상속받아 구현되어있다. Timestamp 와 date 을 eqauls 할경우 대칭성을 위한하고 있어서 같이 혼용되어 사용하면 엉뚱한 결과를 초대할수 있다.
     - Abstract 클래스의 서브클래스에는 eqauls 계약을 위해하지 않고 값 컴포넌트를 추가할수 있다.
- 상속을 이용해 equals 를 구현하려면 instanceof 키워드를 사용해 체크해주는것이 좋다.
- float double 이 아닌 기본 필드경우에는 == 연산자로 비교하며 필드가 객체 참조일때는 eqauls 메소드를 재귀적으로 다시 호출한다. Float 객체의 경우 Float.compare Dobule 은 Double.compare 메소드를 사용한다. Float.eqauls 나 Double.equals 를 사용해도 된다. 객체가 null 을 가질수도 있으므로

{% highlight java linenos %}
(field == null ? o.field == null field.equals(o.field))
{% endhighlight %}
- equals 메소드의 인자타입을 Object 대신에 다른 타입으로 바꾸지 말자! 무조건 기본형을 오버라이딩 하자. 변수타입을 바꿔 오버로드 하면 가끔은 성능향상을 가져올수 있지만 코드만 복잡하게 할 뿐이다. 이런 실수를 줄이기 위해 equals 함수에는 무조건 @Override 키워드를 사용해 주는것이 좋다.

<!-- more -->


### ITEM9 : eqauls 메소드를 오버리이드 할때는 hashCode 메소드도 항상 같이 오버라이드 하자
- equals 를 오버라이드 할떄 hashCode 를 적절하게 오버라이드 해주지 않으면 HashMap HashSet HashTable 을 포함 하는 모든 hash 기반의 컬랙션들과 우리 클래스를 같이 사용할 때 적절하게 동작하지 않을수있다! (해쉬 기반 콜랙션들은 hashCode 함수로 키값을 사용한다)
     - hashCode 는 실행중에 같은 객체에 대해 항상 같은값 호출해야된다. 어플리케이션 재실행때는 달라도됨
     - eqauls 가 true 이면 hashCode 는 반드시 같다
     - eqauls 가 false 라고 해서 hashCode 가 반드시 false 여야 하는건 아니다. 하지만 달라야 해쉬 콜랙션들의 성능을 향상 시킬수 있다.
- 적절한 해쉬 메소드를 사용하여 hashCode 함수를 구현해주어야한다.
     - f 가  boolean 이면 1 or 0
     - f 가  byte char short int 이면 (int)f
     - f 가  long 이면 (int)(f ^ ( f >>> 32))
     - f 가  float 이면 Float.floatToIntBits(f)
     - f 가  double 이면 Double.floatToLongBits(f) 한후에 long 처럼 해준다.
     - f 가 객체 참조일 경우 eqauls 메소드를 재귀적으로 호출하면 hashCode 메소드도 재귀적으로 자동 호출된다.
     - f 가 배열이라면 배열의 각 요소를 별개의 필드처럼 처리한다. Arrays.hashCode 메소드들 중 하나를 사용할수 있다.
     - result = 31 * result + c, result 초기값 17 소수를 사용해서 해쉬 기능 향상 시킴!
     - equals 메소드에서 비교하지 않은 필드는 hashCode 필드에서 제외시켜야함.
     - 만약 해쉬 코드 연산비용이 중요한 클래스라면 lazy initializtion 을 할수있다.
{% highlight java linenos %}
@Override public int hashCode() {
  int result = 17;
  result = 31 * result + areaCode; // 모두 short 형
  result =31 * result + prefix;
  result = 31 * result + lineNumber;
  return result;
}
{% endhighlight %}

{% highlight java linenos %}
private volatile int hashCode;
@Override public int hashCode() {
    int result =hashCode;
    // 한번만  해쉬코드 설정해주고 계속 사용
    if (result == 0) {
      int result = 17;
      result = 31 * result + areaCode; // 모두 short 형
      result =31 * result + prefix;
      result = 31 * result + lineNumber;
      hashCode = result;
    }
    return result;
}
{% endhighlight %}


### ITEM10 : toString 메소드는 항상 오버라이드 하자
- java.lang.Object 클래스는 toString 메소드를 구현하고 있다. 16진수형태로 해쉬코드가 붙는다!
- toString 은 간결하며 읽기 쉬워야 한다. 모든 서브 캘르스들은 이 메소드를 오버라이드 할것을 권한다!
- 표현형식의 규정 여부와는 무관하게 아무튼 그의도를 명쾌하게 문서화 해야한다.



### ITEM 11 : clone 메소드는 신중하게 오버라이드 하자
- Cloneable 인터페이스는 복제를 허용하는 객체라는것을 알리는 목적으로 사용하는 믹스인 인터페이스 이다. 하지만 자신이 clone 메소드를 가지고 있지 않으며 Object 클래스의 clone 메소드는 사용에 제약이 있다. (아무런 추상 메소드를 가지고 있지 않다)
- Cloneable 인터페이스가 비록 clone 함수를 가지고 있지는 않지만 구현하지 않으면 protected clone 함수에서 CloneNotSupportedException 예외가 발생한다.(정장적인 인터페이스 사용법과 다르다. 특별히 protected 메소드의 동작 여부를 결정함)
     - Object 를 상속한 클래스에서 implement Clonable 을 해주지 않아도 Object 의 clone 함수가 불러지지 않는다면 CloneNotSupportedException 발생 안할수도 있다.
- 복제를 할때는 어떤 생성자도 호출되지 않는다.
     - x.clone() != x
     - x.clone().getClass() == x.getClass()
     - x.clone().eqauls(x) == true // 항상 그런거는 아님.
- 수퍼 클래스의 clone 메소드를 오버라이드 할 경우 서브 클래스의 clone 에서는 반드시 super.clone 을 호출하여 얻은 객체를 반환해야 한다.
- java 1.5 이상해서는 제네릭의 일부기능으로 convariant return type 기능이 추가됬다. 서브 클래스의 오버라이딩한 메소드에서는 반환 객체의 더많은 정보를 제공할수 있으며 클라이언트 코드에서는 반환 객체를 서브 클래스 타입으로 캐스팅 할 필요없다.

{% highlight java linenos %}
// object clone() 이 아니여도 된다. return 타입에 되서는 Genric 기능이 제공됨!
@Override public PhoneNumber clone() {
  try {
    return (PhoneNumber) super.clone();
  } catch(CloneNotSupportedException e) {
    throw new AssertionError(); //여기서는 이 예외가 생길 수 없다
}
{% endhighlight %}
- Stack 과 같은 콜랙션을 clone 할 경우
{% highlight java linenos %}
@Override public Stack clone() {
  try {
    Stack result = (Stack) super .clone();
    result.elements = elements.clone();
  } catch(CloneNotSupportedException e) {
    throw new AssertionError(); //여기서는 이 예외가 생길 수 없다
 }
{% endhighlight %}
- 고급 프로그래머가 아니라면 clone 을 사용하지 않고 new TreeSet(s) 형태처럼  clone 없이 변환 생성자를 사용하는것이 좋다.
- Cloneable 은 배열 복제 정도아니면 되도록이면 clone 메소드를 전혀 오버라이드 하지 않고 호출도 안하는게 좋다.



### ITEM 12 : Comparable 인터페이스의 구현을 고려하자
- compareTo 는 java.lang.object 에 구현되어있는게 아니라 Comparable 인터페이스에 있다.
- Comparble 인터페이스를 구현하면 자바의 수많은 알고리즘 및 컬랙션 클래스들과 상호 연동이 가능하다. 자바 라이브러리의 모든 값 클래스들은 Comparable 인터페이스를 구현한다.
  - 작으면 음수 크면 양수 같으면 0 비교할수없으면 **ClassCastException** 을 발생시킨다.
- eqauls 와 같은 일관된 결과를 갖도록 하는것이 좋다.
     - new BigDecimal("1.0") 과 new BigDecimal("1.00") 두 개의 객체를 HashSet 에 추가한다면 두객체 모두 저장될것이다. 두 BigDecimal 객체가 다른것으로 판정되기 때문 (hashCode 다름). 하지만 TreeSet을 사용한다면 CompareTo 메소드를 사용해서 비교하므로 BigDecimal 객체가 같은것으로 판정된다.
     - 정렬 컬랙션들(내부적으로 정렬을 하는, TreeMap, TreeSet )은 컬랙션 인터페이스(Collection, Set, Map) 등의 보편적 계약을 따르지 않을수 있다. 컬렉션 인터페이스의 보편적인 계약은 eqauls 를 메소드 관점에서 정하고 정렬컬랙션들은 **eqauls 대신 compareTo** 로 동일 여부를 검사하는데 사용함.
- compareTo 메소드 내부에서 비교 객체의 타입을 확인하거나 타입 변환 할 필요없다. 타입이 잘못되면 당연히 컴파일 에러가 발생하게 되어있다.
- 자연율 순서와 다른 순서매김을 사용할 필요가 있다면 Comparator 인터페이스를 대신하여 사용할수 있다.
- compareTo 가 음수를 리턴할수는 있지만 -2^31 ~ 2^31-1 범위 안에 값을 리턴해야한다! (오버 플로우가 날 경우 엉뚱한 결과가 나올수 있다.)
