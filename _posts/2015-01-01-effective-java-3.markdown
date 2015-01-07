---
layout: post
title:  "Effective Java 4장 클래스와 인터페이스"
date:   2015-01-01 18:07:40 +0900
categories: Java
tags: Java effective-java
description: 클래스와 인터페이스 (13~21)
---


## 클래스와 인터페이스 (13~21)

### ITEM 13 : 클래스와 멤버의 접근 권한은 최소화 하라
- 정보은닉 캡슐화 : 구현 세부사항을 전부 API 뒤쪽에 감춘다.
     - 정보은닉이 좋은 성능을 보장하는건 아니지만 성능문제를 일으키는지 프로파일링 하는데 용의하다.
     - 각 클래스와 멤버는 가능한 접근 불가능 하도록 만들어라
- 클래스 접근 권한자
     - package-private(deafault)
          - 해당 패키지 안에서만 유효한 객체
          - **최상위 레벨클래스나 인터페이스는 가능한 package-private 로 선언해야 한다.** 다음번 릴리즈때 변경 삭제가 용의하다.
     - public
          - 전역적 객체가 된다.
          - 호환성을 보장하기 위해 해당 개체를 계속 지원해야 한다. 가능한 package-private 를 사용할것
     - private nested class
          - 클래스를 사용하는 클래스가 하나뿐이라면 private 중첩 클래스(nested class) 로 선언해야 한다.  하나의 클래스만이해당 클래스의 접근 권한을 가지게 된다.

- 필드,메서드,중첩 클래스,중첩-인터페이스 접근 권한자
     - private < package-private < protected < public
          - private : 선언된 멤버는 선언된 최상위 클래스 내부에서만 접근 가능
          - package-private : default access, 같은 패지지 내의 아무 클래스에서나 호출이 가능하다.
          - protected : 선언된 클래스의 하위 클래스만 사용할수 있다. package-private 처럼 같은 패키지에서는 사용 가능
               - public 클래스에서 protected 선언이 되어있다면 공개 API로 영원히 유지해야한다.
          - public : 어디에서도 사용이 가능
     - Serializeable 을 구현하는 클래스의 멤버라면 공개 api 속으로 세어 나갈수 있다..
     - 상위 클래스 메서드를 재정의 할떄 원래 메서드의 접근 권한보다 낮은 권한을 설정할수 없다.
          - 상위 클래스에서 사용할수 있는것은 하위 클래스에서도 사용이 가능해야 한다.
          - 따라서 모든 인터페이스 함수는 생략해도 public abstract 이다.
     - 객체 필드는 절대 public 선언 금지, 변경 가능 public 필드를 가진 클래스는 다중 스레드에 안전하지 않다.
          - final public 변수라고 해도 공개 API 가 되므로 바꾸기 쉽지 않아진다. 삭제하거나 수정도 어려움
     - public static final 상수(대문자와 _ 로 구성된 변수이름) 는 변경 불가능 객체를 참조해야한다.
          - 참조 자체는 변경할수 없지만 참조 대상 객체는 변경 할 수 있으므로 문제가 된다.
          - public static final 배열 필드를 두거나 배열 필드를 반환하는 접근자를 정의하면 안된다.

{% highlight java linenos %}
public static final Thing[] VALUES={..}; // VALUES 는 변하지 않지만 VALUE 안에 데이터가 변할수가 있다.
// 해결책1 : 수정 불가능한 List 리턴
private static final Thingp[ PRIVATE_VALUES = {..};
public static final List<Thing> VALUES=Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
// 해결책2 : 복사 반환
public static final Thing[] values(){
     return PRIVATE_VALUES.clone();
}
{% endhighlight %}
- 접근 권한은 가능한 낮추라. 최소한의 public API를 설계한 다음 다른 모든 클래스 인터페이스 멤버는 API 에서 제외해라 public static final 필드를 제외한 어떤 필드도 public 으로 선언 금지, public static final 필드가 참조하는 객체는 변경 불가능으로 해라.


### ITEM 14 : public 클래스 안에서 public 필드를 두지말고 접근자 메서드를 사용해라
{% highlight java linenos %}
// 이런 클래스는 절대로 public 변수 선언 하지마라
class Point{
     public double x;
     public double y;
}
{% endhighlight %}
- public 클래스 즉 선언된 패키지 밖에서도 사용 사능한 클래스 에서는 접근자 메서드를 제공해라
- package-private private 중첩 클래스는 데이터 필드를 공개하더라도 잘못이라 말할 수 없다.
- 변경 불가능 필드는 public 으로 변수 선언을 하더라도 괜찮기는 하지만 추천되지는 않는다.


### ITEM 15 : 변경 가능성을 최소화 하라
- 변경 불가능 클래스는 그 객체를 수정할수 없는 클래스
     - String, 기본 자료형 클래스, BigInteger, BigDecimal
- 변경 불가능 객체 만들때 규칙
     - 1.객체 상태를 변경하는 메서드를 제공하지 않는다
     - 2.상속이 불가능하도록 ( 클래스를 final 로)
     - 3.모든 필드를 final 로 선언 - 동기화 없이 다른 스레드로 전달되어도 안전하다.
     - 4.모든 필드를 private로 선언한다.
     - 5.변경가능 컴포넌트에 대한 독점적 접근권을 보장 - 방어적 복사본을 만들어야 한다.

- 변경 불가능 객체 ex)

{% highlight java linenos %}
public final class Complex{
     private final double re;
     private final double rm;
     public Complex(double re, double im){
          this.re = re;
          this.im = im;
     }
     public double realPart(){ return re; }
     public double imagiaryPart() { return im; }

     public Complexd add(Complex c) {
          return new Complex(re + c.re, im + c.im);
     }
     ...
}
{% endhighlight %}
- add 함수에서 this 객체를 변경하는 대신 새로운 Complex 객체를 만들어 반환 하도록 구현되어 있다.
     - 함수형 접근법이다. 피연산자를 변경하는 대신 연산을 적용한 결과를 새롭게 만들어서 반환한다.
     - 반대로 절차적 명력형 접근법은 피연산자에 일정한 절차를 적용하여 그 상태를 바꾼다.
     - 함수형 접근법은 변경 불가능 객체를 만들수 있도록 해서 장점이 많다.
          - 동기화 필요없이 스레드 안전
          - 자유롭게 공유
          - 내부에서도 공유, BigInteger 에 negate 함수는 내부적으로 int 배열을 참조한다.
          - 맵의 키 집합의 원소로 활용하지 좋다.
          - 단점은 별도의 객체를 만들어야 한다는 점이다.
               - BigIntger 에 하위 비트만 다르더라도 다른 객체를 만들어야한다. 생성 비용이 높다.

- 변경 가능한 package-private 동료 클래스를 사용하면 변경 불가능 객체의 단점을 보완할수 있다.
     - String -> StringBuilder
- final 클래스로 선언하는 방법 말고도 private 생성자를 사용하고 factory 메소드를 제공하는것도 좋은 방법이다.

{% highlight java linenos %}
private(double re, double im){
     ...
}
public static Complex valueOf(double re, double im){
     return new Complex(re, im);
}
{% endhighlight %}
 - BigInteger, BigDecimal 은 final 로선언이 안되서 상속이 가능하도록 잘못 선언되어있다. 따라서 안전하게 변경 불가능한 객체로 사용하려면 상속되어있는 객체인지 확인해야 한다.

{% highlight java linenos %}
public static BigInteger safeInstance(BigInteger val){
     if (val.getClass() != BigInteger.class)
          // val 이 상속이 되어 잘못사용되어진 객체라면
          return new BigInteger(val.toByteArray());
     reutrn val;
}
{% endhighlight %}

- 가끔 성능 향상을 위해 불가능 객체도 비 final 필드를 가진다. 시간이 많이 걸리는 계산 결과를 캐시 해두는 비 final 필드를 가지기도 한다.
     - hashCode 메서드는 처음으로 호출되는 순간에 계산한 해시 코드를 나중에 다시 호출될 경우를 대비해서 캐시한다. **초기화 지연 기법**


### ITEM 16 : 계승하는 대신 구성하라
- 인터페이스 상속을 이야기하는건 아니다 ( 인터페이스를 extends 하는)
- 메서드 호출과 달리 계승은 캡슐화 원칙을 위반한다.
     - 상위 클래스의 구현은 릴리즈가 거듭되면서 바뀔수 있는데 하위 클래스 코드는 수정된적이 없어도 망가질수 있다.
- Hashtable Vector 컬렉션 프레임워크에 넣는 과정에서 보안문제로 수정됨
- 기존 메서드 재정의, 새 메서드로 만들기 둘다 위험하다.
- 상속하는 대신 private 필드를 두는 방법이 좋다
     - 이런 설계법을 구성(composition) 이라고 부름, 기존 클래스가 새 클래스의 일부(Component) 가 되기때문이다.
     - 구성을 통해서 메서드 가운데 필요한 것을 호출해서 그결과를 반환하도록 한다. 이런 구현기법을 전달(forwarding)
     - 기존 클래스의 구현 세부사항에 종속되지 않으므로 구성 기법이 좀더 견고하다

{% highlight java linenos %}
// wrapper 포장 클래스
// Set 에 추가되는 갯수를 알아내는 클래스
public class InstrumentSet<E> extends ForwardingSet<E> {
     private int addCount = 0;
     public InstrumentSet (Set<E> s){
          super(s);
     }
     @Override public boolean add(E e){
          addCount++;
          return super.add(e);
     }
     ..
}
// 재사용 가능한 전달(forwarding) 클래스
// 상속대신 구성을 사용한다.
public class ForwardingSet<E> implements Set<e>
{
     private final Set<E> s; // 중요!! - 구성을 사용
     public ForwardingSet(Set<E> s){ this.s = s;}

     // Set 인터페이스 구현
     public void clear(){ s.clear();}
     public void add(E e){ return s.add(e); }
     ...
}
{% endhighlight %}
- Set의 기능을 사용하면서(구성과 인터페이스를 사용해서) 기능추가(Wrapper 클래스)가 가능
- 인터페이스를 상속했기때문에 이미 있는 생성자도 그대로 사용할수 있다.
     - Set<Date> s = new InstrumentedSet<Date>(new TreeSet<Date>(cmp));

- 구성과 전달을 사용한것을 **위임(delegation)** 이라고 부른다.
- 포장클래스 이런구현기법을 **장식자(decorator)** 패턴이라고 한다?
     - 내가 보기에는 Adapter 패턴 같은데?
     - 포장 클래스에는 단점이 거의 없지만 역호출(callback) 과 함께 사용하기에 적합하지 않다.
          - 포장된(wrapperd) 객체(ForwardingSet) 은 포장 객체(InstrumentedSet) 에 대해 모르기 때문에 자기자신에 대한(this) 를 전달한다. 따라서 역호출 과정에서 포장 객체는 제외된다. 이런문제를 SELF 문제
          - 전달 메소드는 호출 과정에서 성능이 저하되거나 포장객체 때문에 메모리 사용량이 늘어날것 같지만 미약하다.
          - 전달 메서드 코딩은 지루한 작업이지만 전달 클래스는 인터페이스 별로 한번씩만 구현해 놓으면 되고 실제로 인터페이스와 같은 패키지에 이미 들어가 있는 경우가 많다.
- IS-A 관계일때문 상속을 사용하고 아니면 사용하지마라
     - Stack 은 Vector 가 아니지만 계승해서 구현해 놓았다. 문제가 많으므로 사용하지 마라


### ITEM 17 : 계승을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 계승을 금지하라
- 재정의 가능 메서드를 내부적으로 어떻게 사용하는지 반드시 문서에 남겨라
     - public proetected, 비-final메서드

- 클래스 내부동작에 개입할수 있는 hooks 을 신중하게 고른 protected 메서드 형태로 제공해야 한다.
     - protected 멤버 개수는 가능한 줄여야 한다. 너무적으면 상속해서 쓰기에 문제가 생길수 있다.
     - 계승을 위해 설계한 클래스를 테스트하려면 하위 클래스를 직접 만들어서 테스트 해봐라
- **생성자는 재정의 가능 메서드를 호출해서는 안된다.** 상위 클래스 생성자는 하위 클래스 생성자 보다 먼저 실행되므로 하위클래스에서 재정의한 메서드는 하위 클래스가 실행되기전에 호출된다.

{% highlight java linenos %}
public class Super{
     public Super(){
          overrideMe();
     }
     public void overrideMe(){

     }
}
public class Sub extends Super{
     private Date date;
     public Sub(){
          date = new Date();
     }
     @Override
     public void overrideMe(){
          System.out.println(date); // 두번호출되고 null 이찍힘
     }
}
// 결과
// null
// Date
{% endhighlight %}
- Clonable, Serializable 인터페이스를 사용할 경우 계승용 클래스를 설계하기 까다롭다
     - clone, readObject 메서드 안에서 직접적이건 간접적이건 재정의 가능한 메서드를 호출하지 않도록 해야함. 하위 클래스 객체의 상태가 완전히 역직렬화 되기전에 해당 메서드가 실행되어 버린다. 클론의 경우도 복사본 객체의 상태를 미처 수정하기도 전에 해당 메서드가 실행됨
- 상속에 맞도록 설계되어있지 않으면 하위 클래스 생성을 금지하는게 좋다.
     - 클래스를 final 로 선언
     - 모든 생성자를 private, package-private 로 생성하고 public 정적 메서드 추가.
     - Set List Map 처럼 클래스의 정수를 포착하는 인터페이스를 구현하고 있다면 상속을 무조건 안되게 하는게 좋다.


### ITEM 18 : 추상 클래스 대신 인터페이스를 사용하라
- 인터페이스는 믹스인(min-in)을 정의하는데 이상적이다.
     - 믹스인은 클래스가 주 자료형 이외에 추가로 구현할 수 있는 자료형으로 어떤 선택적 기능을 제공한다는 사실을 선언하기 위해 쓰인다.
     - Comparable 은 어떤 클래스가 자기 객체는 다른 객체와의 비교 결과에 따른 순서를 갖는다고 선언할때 쓰는 믹스인 인터페이스이다.
- 인터페이스는 비계층적인 자료형 프레임 워크를 만들수 있도록 한다.
     - Singer SongWriter 가수와 작곡가를 동시에 갖는 SingerSongWriter 도 있을수 있으므로 class 로 구현하기 보다는 interface로 구현하는게 좋다.
     - 인터페이스를 쓰지 않으면 필요한 속성 조합마다 별도의 클래스를 만들어야 한다.
- 추상 골격 구현(abstracgt skeletal implementation) 클래스를 중요 인터페이스 마다 두면 인터페이스의 장점과 추상클래스의 장점을 결합할수 있다. 인터페이스로는 자료형을 정의하고 구현하는 일은 골격 구현 클래스에 맡기면 된다.
     - Abstract-Interface 형식으로 이름을 만듬
     - Collection Framework (AbstractCollection, AbstractSet 등등)

{% highlight java linenos %}
static List<Integer> intArrayAsList(final int[] a){
     if(a == null) throw new NullPointerException();
     // 골격 클래 사용
     return new AbstractList<Integer>(){
          public Interger get(int i){
               return a[i];
          }
          ...
     }
}
{% endhighlight %}
- int 배열을 Integer 객체의  리스트처럼 볼수 있도록 하는 Adatper 패턴
- 골격구현의 작은 변종 가운데 하나로 간단구현(simple implementation)이 있다.
     - AbstractMap.SimpleEntry
     - 계승을 고려해서 만들어진 클래스, 골격 구현과 같지만 추상 클래스가 아니다.
     - 실제로 동작하는 구현체 가운데 가장 간단한 형태
- 추상 클래스가 인터페이스 보다 좋은 점은 추상클래스가 발전 시키기 쉽다는것이다.
     - 인터페이스는 메서드를 추가하면 인터페이스를 사용하는 모든곳에 컴파일 에러가 발생한다(반드시 구현해야하며 구현하지 않으면 추상 클래스가 되야함)
     -


### ITEM 19 : 인터페이스는 자료형을 정의할 때만 사용하라
- 상수 인터페이스(인터페이스 안에 메서드가 없고 static final 필드만 있다)는 상수 이름앞에 클래스 이름을 붙이는 번거로움을 피하기 위해 사용하지만 상수 인터페이스 패턴은 인터페이스르 잘못 사용한것이다.
     - 공개 API로 사용하게 되서 나중에 없애거나 할수가 없어진다.
          - 공개해야만할 상수라면 따로 유틸 클래스를 만들어서 공개
     - 모든 하위 클래스에 이름공간이 해당 인터페이스의 상수들로 오염된다.
     - 자바 플랫폼 라이브러리에도 상수인터페이스가 있다.(java.io.Object.StreamConstants)
     - 상수앞에 클래스이름을 붙이는것이 귀찮다면 JDK1.5 부터 제공하는 정적 임포트를 사용해라(InnerClass 임포트)


### ITEM 20 : 태그 달린 클래스 대신 클래스 계층을 활용하라
- 두가지 이상의 기능을 가지고 있으며 그중 어떤 기능을 제공하는지 표시하는 태그가 달린 클래스

{% highlight java linenos %}
// 사각형 원 두가지 기능을 가진 태그 달린 클래스
class Figure{
     enum Shape{ RECTANGLE, CIRCLE}
     final Shape shape;
}
{% endhighlight %}
- enum 선언 태그 필드 switch 문등 상투적 코드가 반복된다. 객체를 만들때마다 필요없는 기능을 위한 필드도 함께 생성 되서 메모리 낭비한다. 즉 오류 발생 가능성이 높고 효율적이지도 않다.
- 추상클래스로 선언하고 상속을 이용하는게 좋은방법

{% highlight java linenos %}
abstract class Figure{
     abstract double area();
}
class Circle extends Figure{
}
class Retangle extends Figure{
}
{% endhighlight %}
- 컴파일 시에 형 검사가 가능하고 변경 확장에도 용의하다.


### ITEM 21 : 전략을 표현하고 싶을 때는 함수 객체를 사용하라
- 프로그래밍 언어 가운데 함수 포인터, 대리자, 람다 표현식 처럼 특정 함수를 호출할 수 있는 능력을 저장하고 전달 할수 있는 것들이 있다.
     - 비교자 함수를 통해 정렬을 함수를 한다. 전략패턴이라고 부른다.
- ~~자바는 함수포인터를 지원하지 않는다~~  java8 부터 지원

{% highlight java linenos %}
// 스트링을 비교하는 전략 클래스
class StringLengthComparator implements Comparator<String>{
     private StringLengthComparator(){}
     // 무상태 클래스(변경 가능한 필드가없다) 이므로 싱글턴 패턴으로 쓸데 없는 객체 생성을 피하도록, 한번만 생성하도록 한다.
     public static final StringLengthComparator INSTANCE= new StringLengthComparator();
     public int compare(String s1, String s2){
          return s1.length() - s2.length();
     }
}
{% endhighlight %}
- 실행 가능 전략 클래스는 굳이 public 으로 만들어 공개할 필요가 없다. public static 필드드들을 갖는 호스트 클래스를 정의한다. 호스트 클래스의 private nessted class 로 정의 하면 된다.


### ITEM 22 : 멤버 클래스는 가능하면 static 으로 선언하라.
- 중첩 클래스 - 다른 클래스 안에 정의된 클래스
     - 정적 멤버 클래스(static member class)
     - 비정적 멤버 클래스(non static member class)
     - 익명 클래스(anonymous class)
     - 지역 클래스(local class)
     - 정적 멤버 클래스 제외한 나머지는 내부(inner class)이다.
- 정적 멤버 클래스
      - 다른 클래스 안에 선언된 일반 클래스
      - 바깥 클래스의 모든 멤버(private 까지) 접근 가능하다.
      - 정적 멤버 클래스를 private 로 선언했다면 해당 중첩 클래스에 접근할수 있는 것은 바깥 클래스 뿐이다.
      - 바깥 클래스와 함께 사용할때만 유용한 public 도움 클래스 만들때 사용
      - Map 의 Map.Entry 객체, 특정 맵의 컴포넌트로 맵객체에 속하지만 맵 객체에 접근할 필요가 없다.

- 비정적 멤버 클래스
     - 바깥 클래스 객체와 자동으로 연결, 비정적 멤버 클래스 안에서는 바깥 클래스의 메서드를 호출할 수도 있고, this를 통해 바깥객체에 대한 참조를 획득할수도 있다.

{% highlight java linenos %}
class Envelope{
    void x(){ System.out.println("hello")}
    class Enclosure{
         void x() {Envelop.this.x(); /*한정됨*/}
    }
}
{% endhighlight %}

- 중첩된 클래스의 객체가 바깥 클래스 객체와 독립적으로 존재할수 있도록 하려면 정적 멤버 클래스로 선언해야한다. **비정적 멤버 클래스는 바깥 클래스의 객체없이는 존재할 수 없다.**
     - 비정적 멤버 클래스의 객체가 만들어 지는 순간 바깥 클래스와 연결이 생긴다.
          - 바깥 클래스의 메소드에서 비정적 클래스를 생성하는 순간
          - 드물게 enclosingInstance.new MemberClass(args) 처럼 만들기도 한다.
          - Adapter 를 정의할때 많이 사용한다

{% highlight java linenos %}
public class MySet<E> extends AbstractSet<E>{
     public Iterator<E> iterator(){
          return new MyIterator();
     }
     // 비정적적 멤버 클래스
     // MySet 에 클래스에 접근이 가능해야하므로
     private class MyIterator implements Iterator<E>{
          ...
     }
}
{% endhighlight %}

- 바깥 클래스 객체에 접근할 필요가 없는 멤버 클래스를 정의할때는 항상 선언문앞에 static 을 붙여서 정적 멤버 클래스로 만들어라.
- 정적 클래스는 바깥객체에 참조를 유지하므로 시간 메모리 공간 요구량이 늘어나고 가비지 컬랙션도 힘들어진다.

- 익명 클래스
     - 함수 객체를 정의할떄 널리쓰인다.
     - Runnable Thread 같은 프로세스 객체를 만드는 데도 널리 쓰인다.
     - 비정적 문맥(nonstatic context) 안에서 사용될때만 바깥 객체를 갖는다. 정적문맥안에서 사용된다 하더라도 static 멤버를 가질수는 없다.
     - 해당 중첩 클래스의 특성을 규정하는 자료형이 이미 있다면 익명클래스로 만들어라

- 지역 클래스
     - 지역 변수가 선언될수 있는 곳이라면 어디든 선언할수 있다. 지역변수와 동일한 유효범위 규칙
