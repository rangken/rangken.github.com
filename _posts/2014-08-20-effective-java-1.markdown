---
layout: post
title: "Effective Java 2장 객체의 생성과 호출"
date: 2014-08-20 18:07:40 +0900
categories:
redirect_from: "{{page.url}}"
description: Effective-Java 객체의 생성과 호출
---

## Effective-Java 객체의 생성과 호출 (1~7)

### ITEM1 : 기본 생성자보다 Static 팩토리 메소드를 만들어라.
- 클래스의 인스턴스(instance) 을 생성하도록 하는 일반적인 방법은 public 생성자(constructor)을 제공하는 것이다.
- boolean primitive 값을 가져와서 Boolean 객체 참조로 변환하여 반환하는 Static Factory
{% highlight java linenos %}
public static Boolean valeOf(boolean b){
     return b ? Boolean.TRUE : Boolean.FALSE;
}
{% endhighlight %}
- Static Factory 장점
  - 기본 생성자와는 다르게 메소드에 이름을 지어줄수 있다.
    - 생성자에 전달되는 매개변수가 반환 객체를 잘 나타내지 못하기 때문에, 이름을 잘지은 static 팩토리 메소드가 더 좋음
    - ex) **BigInteger** 클래스에서 소수를 생성하는 **probablePrime** Static 팩토리 메소드
  - 매번 새로운 오브젝트를 생성하지 않아도 된다.
    - imutable 불변 클래스의 경우 이미 생성된 인스턴스를 다시 사용할수 있다.
    - ex) 싱글톤 팩토리 함수, ( *같은 클래스 생성으로 인해 equals 를 매번 오버라이딩 해주지 않고 그냥 == 로 비교가능함* )
    - ex) **Boolean.valueOf(boolean)** 함수는 매번 새로운 인스턴스를 생성하지 않는다. ( *new Boolean(true) 로 한다면 매번 새로운 객체를 생성할 것이다.* )
  - 자신이 반환하는 타입의 어떤 서브 타입 객체도 리턴 가능함
    - ex) **Interface-Based framework, Java Collection Framework interface** - EnumSet 크기가 커지면 내부적으로 EnumSet 을 상속시킨 JumboEnumSet 을 팩토리 메소드에서 생성해서 알아서 리턴해줌
    - ex) **Service Provider framework** 클래스가 작성되는 시점에 그 메소드로 부터 반환되는 객체의 클래스가 존재하지 않아도 된다.
  - 생성자에 변수 타입을 일일이 입력해주어야 하는 불편함을 줄여줄수 있다.
{% highlight java linenos %}
Map<Strng, List<String>> = new HashMap<String, List<String>>();
// 아래 처럼 사용 가능하도록!
public static <K,V> HashMap<K, V> newInstance() {
  return new HashMap<K, V>();
}
{% endhighlight %}

  - 생성자에 호출시에 타입 추론처리는 1.6에서는 안된다. 1.8은 되나??
- Static Factory 단점
    - 인스턴스 생성을 위해 static 팩토리 메소드만 갖고 있으면서 public이나 protected 생성자가 없는 클래스의 경우는 서브 클래스를 가질 수 없다
    - 다른 여러 스태틱 메소드랑 구별하기 쉽지 않고 코드를 읽기도 쉽지 않음 ~~javadoc 으로 문서화를 잘하고 함수명 convention 을 잘지키면됨~~


- Static Factory Convention : 아래 함수들은 Static Fatory로 사용된다고 규약을 정한다.
    - valueOf : 파라메터와 같은 값을 리턴하는.
    - of
    - getInstance
    - newInstance
    - getType
    - newType

- Static 팩토리 메소드를 먼저 고려해보고 무심코 public 생성자를 만드는 습관을 버려라.


### ITEM2 : 생성자의 매개변수가 많을때는 차라리 빌더를 만들어라.
- 많은 변수를 가지면 Static Factory 나 생성자나 모든 초기화 함수를 만들어 주는데(*telescoping construntor*)는 한계가 있다.
- 생성자를 간단하게 만들기 위한 방법으로는 Setter 메소드를 만들어주는 *JavaBean* 패턴이 있다. 초기화를 한후에 setter 로 값을 다 넣어준다.
  - 여러번의 메소드 호출로 나누어져 인스턴스가 생성되서 생성과정을 거치는동안 일관된 상태가 유지 되지 못함. ( *모든 초기화가 이루어지기전에 다른 Thread 에서 사용해버릴수도 있다* )
  - immutable 클래스를 만들수 없어진다. 즉 Thread 안정성을 유지 할수가없다. 물론 객체를 freeze 하는 함수를 사용할수 있지만 런타임 에러를 만들어 낼 가능성이 있다.
- java 1.5 이상을 쓴다면 기본 Bulder<T> 인터페이스가 존재한다.
- 빌더를 사용하면 생성자에는 사용할수없는 가변인자(*varargs*) 를 사용할수 있다.
- Class 클래스에는 newInstance 라는 추상 팩토리 메소드가 있는데 newInstance 는 항상 객체가 생성될때 클래스의 매개변수가 없는 생성자를 호출하려고 한다. 만약에 그런 생성자가 클래스에 없다면 컴파일 에러가 발생하지 않고 런타임 에러(*Instantiation-Exception, IllegalAccessException*) 이 발생하므로 컴파일 시점에서 예외 검사를 어렵게 만든다. 빌더를 사용하면 이런 위험을 줄일수 있다.
- 빌더 패턴은 추가적으로 빌더를 생성해야 하므로 생성 비용이 드는 단점이 있다.
{% highlight java linenos %}
// Builder Pattern
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;
    public static class Builder {
        // Required parameters
        private final int servingSize;
        private final int servings;
        // Optional parameters - initialized to default values
        private int calories      = 0;
        private int fat           = 0;
        private int carbohydrate  = 0;
        private int sodium        = 0;
        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings    = servings;
          }
        public Builder calories(int val)
            { calories = val;      return this; }
        public Builder fat(int val)
            { fat = val;           return this; }
        public Builder carbohydrate(int val)
            { carbohydrate = val;  return this; }
        public Builder sodium(int val)
            { sodium = val;        return this; }
        public NutritionFacts build() {
            return new NutritionFacts(this);
          }
     }
    private NutritionFacts(Builder builder) {
        servingSize  = builder.servingSize;
        servings     = builder.servings;
        calories     = builder.calories;
     }
}
// 사용
NutritionFacts cocaCola = new NutritionFacts.Builder(240 ,8). calories(100).sodium(35).carbohydrate(27).build();
{% endhighlight %}
- Builder 는 매개변수가 많이 늘어나거나 가변인자를 사용할 경우 고려해보자. telescoping 방법보다 가독성이 좋고 JavaBeans 보다는 휠씬 안전하다.



### ITEM3 : 싱글톤 만들때 생성자를 Private 로 해야하고 혹은 enum타입을 통해 만들면 좋다.
- 생성자를 private 로 만들어서 해당 클래스 외부에서 따로 생성하지 못하도록 해야함
{% highlight java linenos %}
public class Elvis {
  public static final Elvis INSTANCE = new Elvis(); // static final!!
  private ElvisO { ... } // private 생성자!!
  public void leaveTheBuilding() { .. . }
}
{% endhighlight %}
- enum 싱글톤을 사용하는 이유?
  - enum 없이 싱글톤을 만들면 reflection 을 통해서는 instance 생성자에 접근할수가 있다.
  - 싱글톤에서 serializable 을 사용하려면 instance 변수는 transient 로 선언해야 한다. 매번 deserialize 할때마다 instance 에 새로운값이 생성될수 있으므로 transient 를 통해 serialize 할때 제외하도록 설정해 주어야한다.
- java 1.5 이상을 사용하면 enum 을 통해서 싱글톤을 구현하면 좋다. 여러번 초기화 오브젝트가 생성되거나 serialization, reflection attack 등에 상관없이 구현가능하다.

{% highlight java linenos %}
public enum Elvis{
     INSTNACE;
     private String test = "abc"
     public void leaveTheBuilding(){
          this.test = "ccc"; // INSTANCE.test 도 가능.
     }
}
{% endhighlight %}
  - enum 방식에 싱글톤은 널리 적용되어 있지 않다. 개인적으로도 Serialize 문제가 아니라면 구지 사용할 필요가 없다고 생각된다.


### ITEM4 : PRIVATE 생성자로 인스턴스를 생성할수 없게하라.
- java.lang.math 나 java.util.array 같은 util 클래스는 인스턴스가 생성되도록 하면 안되므로 생성자를 piravte 로 선언하면 인스턴스가 생성되는것을 막을수 있다.
- 기본적으로 생성자를 만들지 않아도 자바 컴파일러가 기본 생성자를 만들기 때문에 명시적으로 private 생성자를 선언해주어야한다.
- 추상 클래스를 사용해서 인스턴스를 생성하지 못하도록 하는 방법을 사용하면 안된다. 추상 클래스를 상속해서 인스턴스를 생성 할 수 도 있고 클래스가 마치 상속을 위해 생성된거처럼 잘못 알게된다.
- private 생성자를 만들면 subclass 를 만들수 없으므(*컴파일 에러 발생*)로 항상좋은건 아니다.
{% highlight java linenos %}
public class UtilityClass {
  // 디폴트 생성자가 만들어지는것을 방지
  private UtilityClass() {
    // 혹시나 불러질 가능성 대비해서 (클래스 내부에서) 에러 발생
    throw new AssertionError();
  }
}
{% endhighlight %}

### ITEM5 : 필요없는 오브젝트가 생성되는것을 피해라
- Immutable 객체는 항상 재사용이 가능함!!
{% highlight java linenos %}
String s = new String("abc"); // 매번 새로운 객체 생성
String s = "abc"; // "abc" 스트링 풀에서 공부

Boolean.valueOf(String); // 새로운 인스턴스를 생성하지 않지만
new Boolean(String) // 생성자는 새로운 인스턴스를 생성
// Static 팩토리를 사용하는것이 좋다.
{% endhighlight %}
- 가변 객체도 객체의 상태가 변경되지 않는다면 static{ } 블락에서 초기화 한후 재사용하자.자주 호출되는  함수에서 매번 객체를 생성하는 일은 하지 않는게 좋다.
- immutable object 는 매번 인스턴스를 만들지 말고 static 블록에서 초기화한후 공유해서 사용해야 한다.
- Map 에 keySet 함수도 매번 새로운 Set를 만들어서 리턴하지 않고 매번 같은 인스턴스를 리턴함.
- java 1.5 이상에서 제공하는 autoboxing 이 일어나지 않도록 해라 Long 과 primitive long 간에 boxing 이 일어나면 추가적인 오버헤드가 발생한다.
{% highlight java linenos %}
public static void main(String[] args) {
  Long sum =0L;
  for (1ong i = 0; < Integer.MAX_VALUE; i++) {
    sum += 1; // autoboxing 일어남.
    System.out.println(sum);
  }
}
{% endhighlight %}

### ITEM6 : 쓸모없는 Object refreences 를 없애라
- 자바는 C/C++ 과는 다르게 더이상 참조되지 않으면 객체들이 사용하던 메모리가 자동으로 회수된다. **마법은 아니므로 주의!**
{% highlight java linenos %}
  public Object pop(){
      if(size == 0)
           throw new EmptyStackException();
      Object result = elements[--size];
      elements[size] = null; // 쓸모없는 reference 를 제거해주어야한다.
{% endhighlight %}
- null 처리를 해주면 참조를 제거해서 메모리에서 사라지도록 할수있고, 잘못된 참조로 원하지 않게 작동하지 않고 NullPointerException 이 바로 일어나도록 할수있다.
- 클래스가 자기자신의 메모리를 관리할때 프로그래머가 메모리 릭에 대해 조심해야한다. 특정 element 가 free 되면 object reference 들은 모두 null 처리가 되어져야 한다.
- 캐쉬 상황에서 메모리 릭이 발생하기 쉽다. 캐쉬 외부에 캐쉬의 키에 대한 참조가 있을 동안만 캐시에 저장된 항목이 유효한 캐쉬를 구현해야한다. WeakHasMap을 캐시로 사용하면 key값의 외부 참조에 따라 결정되도록 할수있다. (*weak reference*)
- 콜백과 리스너에서도 메모리릭이 발생하기 쉽다. 명시적으로 콜백을 deregister 시키지 말고 weak reference 를 사용하거나 키값들을 weakHashMap 을 사용해 저장하라.



### ITEM7 : finalizer 사용하지 마라.
- Java의 finalizer 는 C++ 의 소멸자가 아니다!
- 신속하게 실행된다는 보장이 없다.
- 혹시나 사용할경우 : 생성된 객체를 종료하는 메소드 호출이 재대로 동작하지 않을경우에 대한 대비 ex) FileInputStream, Timer, Connection 에는 finalizer 가 있다.
