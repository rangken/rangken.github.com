---
layout: post
title:  "Effective Java 6장 열거형(enum)과 제네릭"
date:   2015-01-15 18:07:40 +0900
categories: Java
tags: Java effective-java
description: 열거형과 어노테이션 (30~37)
---

## 열거형과 어노테이션 (30~37)
- Java 에서 열거 자료형(enum type) 은 참조 자료형이다.

### ITEM30 : int 상수 대신 enum을 사용하라
- 열거 자료형(enumerated type)은 고정 개수의 상수들로 값이 구성되는 자료형
- int 대신 enum 을 사용하면 공간적 시간적 손해를 보긴핮만 성능면에서 비등하다
- enum 은 가독성이 좋고 안전하며 강력함.
- 생성자나 멤버가 필요없으나 데이터 또는 그데이터에 관계된 메서드를 추가해서 기능을 향상시킨 enum 도 많다!

- int enum 패턴 : _사용하면안된다_

{% highlight java linenos %}
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPIN =1;
...
int i = (APPLE_FUJI - ORANGE_TEMPLE) / APPLE_PIPPIN; // 변수가 섞임
{% endhighlight %}
- 단점
     - 상수를 사용하는 클라이언트 코드와 함께 컴파일됨, 값이 변경되면 다시 컴파일 다시해야함
     - 크기를 알아낼수 없음
     - 디버깅이 불편함
          - String enum 패턴으로 해결 가능하지만 _더더욱 최악의방법_ 

{% highlight java linenos %}
public enum Apple { FUJI, PIPPIN, GRANNY_SMITH}
{% endhighlight %}
- enum
     - C/C++/C# 언어가 제공하는 enum 이랑 다름, _다른언어에서는 단순 int 값이다_
          - Java 에서는 **참조 자료형**
     - enum 자료형은 public static final 필드 형태로 제공하는것
     - 클라이언트가 접근할수 있는 생성자가 없다. 계승 확장 불가능
     - 컴파일 시점에 형 안정성 제공한다! 같은 enum형 에서 비교연산자 == 가 가능하다!
     - 같은 이름의 상수가 공존 가능.
     - enum 형은 toString 메서드를 호출 가능해서 문자열로 쉽게 변경 가능!
     - Object 에 정의된 메서드들이 포함되어있다.
     - Comparable, Serializable 인터페이스 구현되어있다!

{% highlight java linenos %}
public enum Planet {
     MERCURY (3.302e+23, 2.439e6),
     VENUS .
     EARTH
     MARS
     JUPITER
     SATURE
     URANUS
     NEPTUNE
    
     private final double mass; // 킬로그램 단위
     private final double radius;
     private final dobule surfaceGravity;
    
     // MERCURY (3.302e+23, 2.439e6) 처럼 두개의 값을 초기화 하는 생성자
     Planet(double mass, double radius){
          this.mass = mass;
          this.radius = radius;
          surfaceGravity = G * mass / (radius * radius);
     }
    
     // 접근자
     public double mass() { return mass; }
     ...
    
     // 추가 메서드
     public double surcfaceWight(double mass){
          return mass * surfaceGravity; // F = ma
     }
}

// 사용
double mass = earthWeight / Planet.EARTH.surfaceGravity();
// values 라는 static 함수존재한다! 이외에도 기본 제공함수들이 많음!
for(Planet p : Planet.values() )
     p.surfaceWeight(mass)
    
{% endhighlight %}
- **enum 상수에 데이터를 넣으려면 객체 필드를 선언하고 생성자를 통해 받은 데이터를 그 필드에 저장하면 된다!**
- enum 도 클라이언트에게 공개할 이유가 없다면 private 나 package-private로 선언해야 한다.
- 공개한다면 최상위 public 클래스로 생성하거나, 최상위 클래스의 멤버 클래스로 선언하면 된다.

- **switch 문 대신 상수별 메서드를 구현해야 한다!**

{% highlight java linenos %}
public enum Operation {
     // switch 로 +,-,*,/ 일때 분기해서 처리하는것보다 이런식으로 처리하는게 확장성에 좋다.
     // 새로운 ^ 과 같은 값이 추가된다면 switch 로 하면 문제없이 컴파일 되지만 abstract 방식을 사용하면 컴파일 에러 발생!
     PLUS("+") {
          double apply(double x, double y) {
               return x + y;
          }
     },
     MINUS("-") {
          double apply(double x, double y) {
               return x - y;
          }
     },
     TIMES("*") {
          double apply(double x, double y) {
               return x * y;
          }
     },
     DIVIDE("/") {
          double apply(double x, double y) {
               return x / y;
          }
     };
     private final String symbol;

     Operation(String symbol) {
          this.symbol = symbol;
     }

     @Override
     public String toString() {
          return symbol;
     }

     abstract double apply(double x, double y);

     private static final Map<String, Operation> stringToEnum = new HashMap<String, Operation>();
     static {
          // Operation 생성자 안에서 stringToEnum 에 값을 넣으면 NullPointerException 이 난다.
          // enum 생성자안에서는 enum 의 static 필드를 접근할수 없다. (컴파일 시점에서 상수면 접근 가능)
          // 생성자가 실행될때 static 필드는 초기화된 상태가 아니기 떄문!
          for (Operation op : values())
               stringToEnum.put(op.toString(), op);
     }

     public static Operation fromString(String symbol) {
          return stringToEnum.get(symbol);
     }

     public static void main(String[] args) {
          double x = Double.parseDouble(args[0]);
          double y = Double.parseDouble(args[1]);
          for (Operation op : Operation.values())
               System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
     }
}
{% endhighlight %}
- enum 값에 따라 처리하는 방법2

{% highlight java linenos %}
enum PayrollDay {
     MONDAY(PayType.WEEKDAY), TUESDAY(PayType.WEEKDAY), WEDNESDAY(
               PayType.WEEKDAY), THURSDAY(PayType.WEEKDAY), FRIDAY(PayType.WEEKDAY), SATURDAY(
               PayType.WEEKEND), SUNDAY(PayType.WEEKEND);

     private final PayType payType;

     PayrollDay(PayType payType) {
          this.payType = payType;
     }

     double pay(double hoursWorked, double payRate) {
          return payType.pay(hoursWorked, payRate);
     }

     private enum PayType {
          WEEKDAY {
               double overtimePay(double hours, double payRate) {
                    return hours <= HOURS_PER_SHIFT ? 0 : (hours - HOURS_PER_SHIFT)
                              * payRate / 2;
               }
          },
          WEEKEND {
               double overtimePay(double hours, double payRate) {
                    return hours * payRate / 2;
               }
          };
          private static final int HOURS_PER_SHIFT = 8;
         
          // 추상 메소드! 확장성에 좋다!
          abstract double overtimePay(double hrs, double payRate);

          double pay(double hoursWorked, double payRate) {
               double basePay = hoursWorked * payRate;
               return basePay + overtimePay(hoursWorked, payRate);
          }
     }
}
{% endhighlight %}

### ITEM31 : ordinal 대신 객체 필드를 사용하라
- 모든 enum 에는 ordinal() 이라는 메서드가 있음

{% highlight java linenos %}
public num Ensemble {
    SOLO, DUET, TRIO, QUATET, QUINTET,..
    
    // 절대 만들어서는 안되는 메소드, 타입을 추가하거나 제외할 경우에 매우 취약하다.
    public int numberOfMusicians() { return ordinal() + 1;} 
}
{% endhighlight %}
- ENUM 형 값에 명시적으로 값을 줄수도 있다. 이런경우에 값을 바꿀경우 _numberOfMusicians()_ 함수가 외부에 종속적이므로 문제가 발생할수 있다.

- ENUM 상수에 연계되는 값을 _ordinal_ 을 사용해 표현하지 말아라!!! 그런값이 필요하다면 그대신 객체 필드에 저장해야 한다.

{% highlight java linenos %}
public enum Ensemble {
    // 명시적으로 enum 숫자 정해줌
    SOLO(1), DUET(2), TRIO(3), QUARTET(4), QUINTET(5), SEXTET(6), SEPTET(7), OCTET(
            8), DOUBLE_QUARTET(8), NONET(9), DECTET(10), TRIPLE_QUARTET(12);

    private final int numberOfMusicians;
    
    // 생성 할때 사이즈 받는다!
    Ensemble(int size) {
        this.numberOfMusicians = size;
    }
    // 객체 필드를 리턴함
    public int numberOfMusicians() {
        return numberOfMusicians;
    }
}
{% endhighlight %}
- _ordinal()_ 함수 자체는 _EnumSet_ _EnumMap_ 처럼 일반적인 용도의 enum 기반 자료구조에서 사용할 목적으로 설계한 메서드 이다.
    - _EnumSet_ : 비트 필드 대신에 사용해야하는 자료구조
        - **EnumSet.of(Ensemble.SOLO,Ensemble,DUET)**
    _ _EnumMap_ : 맵에 키값으로 enum 필드를 사용할수 있는 자료구조
        - **EnumMap<Ensemble, String> map = new ..**

### ITEM32 : 비트 필드(bit field) 대신 EnumSet을 사용하라
- 열거 자료형 원소들을 집합에 사용할때 C 스타일로 int eunm 패턴에 2의 거듭제곱 값을 대입해서 사용했다

{% highlight java linenos %}
public class Text{
    public static final int STYLE_BOLD = 1 << 0; //1
    public static final int STYLE_ITALIC = 1 << 1; // 2
    ...
    public void applyStyle(int styles){ ..}
}
text.appleStyles(STYLE_BOLD | STYLE_ITALIC); // 원소들을 집합으로 사용하기 위해서 | 비트 연산을 이용
{% endhighlight %}
- 집합을 비트 필드로 나타내면 비트 단위 산술 연산을 통해 합집합 교집합 등의 집합 연산도 효울적으로 실행할수 있다. **하지만 여러 단점이 존재함!!**
    - 상수를 출력해도 이해하기가 어렵다. 
    - 비트 필드에 포함된 모든 요소를 순차적으로 살펴보기도 어렵다.
- 대신에 **java.util.EnumSet** 을 사용하면 된다. 
    - Set 인터페이스을 구현해서 Set 이 제공하는 풍부한 기능을 그대로 사용할수 있다.
    - 형 안정성, 상호운영성(다른 구조의 Set도 EnumSet으로 바꿀수 있다.) 을 제공함!
    - EnumSet 내부 구현 자체는 bit vector 로 되어있다. enum 값 갯수가 64개 이하이면 long 값 하나만 사용할수 있다.

{% highlight java linenos %}
public class Text {
    public enum Style {
        BOLD, ITALIC, UNDERLINE, STRIKETHROUGH
    }
    // EnumSet 이 아니라 Set 으로 사용가능하게 했다. 
    // 인터페이스를 자료형으로 쓰므로 인해서 상호 운영성을 얻을수 있다.
    public void applyStyles(Set<Style> styles) {
        // Body goes here
    }

    public static void main(String[] args) {
        Text text = new Text();
        // EnumSet 사용!
        text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
    }
}
{% endhighlight %}
- 열거 자료형을 집합에 사용해야 한다고 해서 비트 필드로 구현하지마라!!!
- 자바1.6 기준으로 EnumSet 은 변경 불가능한 객체를 만들수 없다. 
    - _이후 변경될것이다._
    - Java 1.7 에서도 불가능하다. **Guava 를 사용하면 가능하다**

### ITEM33 : ordinal을 배열 첨자로 사용하는 대신 EnumMap을 이용하라
- ordinal 을 사용해 여러 자바 자료구조와 Enum 형을 맵핑 시키는 일은 하면 안된다. 
    - 형 안정성을 제공하지 못하고, 틀린값을 쓰게 되면 이상한 짓을 하게 되거나 해당 Index 를 넘어갈경우 _ArrayIndexOutOfBoundsExeception_ 을 만들수도 있다.
    
- java.util.EnumMap 을 사용하여 enum 상수를 키로 사용하는 맵을 만들자!

{% highlight java linenos %}
public class Herb {
    public enum Type {
        ANNUAL, PERENNIAL, BIENNIAL
    }

    private final String name;
    private final Type type;

    Herb(String name, Type type) {
        this.name = name;
        this.type = type;
    }

    @Override
    public String toString() {
        return name;
    }

    public static void main(String[] args) {
        Herb[] garden = { new Herb("Basil", Type.ANNUAL),
                new Herb("Carroway", Type.BIENNIAL),
                new Herb("Dill", Type.ANNUAL),
                new Herb("Lavendar", Type.PERENNIAL),
                new Herb("Parsley", Type.BIENNIAL),
                new Herb("Rosemary", Type.PERENNIAL) };

        // Enum 형 Herb.Type 을 키로 사용!!
        Map<Herb.Type, Set<Herb>> herbsByType = new EnumMap<Herb.Type, Set<Herb>>(
                Herb.Type.class);
        for (Herb.Type t : Herb.Type.values())
            herbsByType.put(t, new HashSet<Herb>());
        for (Herb h : garden)
            herbsByType.get(h.type).add(h);
        System.out.println(herbsByType);
    }
}
{% endhighlight %}
- 내부적으로는 _ordinal_ 을 사용한다. 명시적으로 _ordinal_ 을 사용하는것과 성능상에 크게 차이가 없다.
- **new EnumMap\<Herb.Type, Set\<Herb\>\>(
                Herb.Type.class)** 
    - 생성자에 키의 자료형을 나타내는 Class 객체를 인자로 받는다!.
    - Class 객체를 한정적 자료형 토큰으로 사용해서 실행 시점에서 제네릭 자료형 정보를 제공한다.
        - EnumMap 은 내부적으로 **new Object[length]** 형식으로 되어있다. 타입을 체크하고 리턴하기 위해서는 타입정보를 알아야한다.
        - EnumSet 은 static factory 로 생성되서 타입 정보를 넘기지 않는거 같지만 내부적으로 _e.getDeclaringClass()_ 를 가져와서 타입정보를 가져온다. 처음 생성시에 타입을 넘기므로 EnumMap 처럼 생성할때 타입정보를 넘기지 않아도 된다.

- 액체 고체 기체 변화를 나타내는 Enum 형 
    - 표현해야 하는 관계가 다차원적이라면 **EnumMap\<... , EnumMap\<...\>\> 과 같이 사용하면 된다.**

{% highlight java linenos %}
public enum Phase {
    SOLID, LIQUID, GAS;

    public enum Transition {
        MELT(SOLID, LIQUID), FREEZE(LIQUID, SOLID), BOIL(LIQUID, GAS), CONDENSE(
                GAS, LIQUID), SUBLIME(SOLID, GAS), DEPOSIT(GAS, SOLID);

        private final Phase src;
        private final Phase dst;

        Transition(Phase src, Phase dst) {
            this.src = src;
            this.dst = dst;
        }

        // 변경 테이블을 맵으로 만든다.
        private static final Map<Phase, Map<Phase, Transition>> m = new EnumMap<Phase, Map<Phase, Transition>>(
                Phase.class);
        static {
            // 가능한 변화를 초기화
            for (Phase p : Phase.values())
                m.put(p, new EnumMap<Phase, Transition>(Phase.class));
            for (Transition trans : Transition.values())
                m.get(trans.src).put(trans.dst, trans);
        }

        public static Transition from(Phase src, Phase dst) {
            return m.get(src).get(dst);
        }
    }

    public static void main(String[] args) {
        for (Phase src : Phase.values())
            for (Phase dst : Phase.values())
                if (src != dst)
                    System.out.printf("%s to %s : %s %n", src, dst,
                            Transition.from(src, dst));
    }
}
{% endhighlight %}
        
### ITEM34 : 확장 가능한 enum을 만들어야 한다면 인터페이스를 이용하라
- ENUM 형은 기본적으로 계승이 안된다!!
    - 계승이 되는거처럼 흉내 내려면 인터페이스를 만들어서 같은 인터페이스를 implements 할수 있다.

{% highlight java linenos %}
public interface Operation {
    double apply(double x, double y);
}
public enum BasicOperation implements Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES("*") {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        public double apply(double x, double y) {
            return x / y;
        }
    };
    private final String symbol;

    BasicOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }
}
{% endhighlight %}

{% highlight java linenos %}
// BasicOperation 처럼 Operation 구현
public enum ExtendedOperation implements Operation {
    EXP("^") {
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    },
    REMAINDER("%") {
        public double apply(double x, double y) {
            return x % y;
        }
    };

    private final String symbol;

    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }

    public static void main(String[] args) {
        double x = Double.parseDouble(args[0]);
        double y = Double.parseDouble(args[1]);
        test(ExtendedOperation.class, x, y);

        System.out.println(); 
        test2(Arrays.asList(ExtendedOperation.values()), x, y);
    }

    // Enum 형을 테스트 하기 위해 한정적 자료형 토큰을 넘기는 방법 
    // Class 에 getEnumConstants 를 사용해 가능한 Operation 을 가져올수 있다.
    private static <T extends Enum<T> & Operation> void test(Class<T> opSet,
            double x, double y) {
        for (Operation op : opSet.getEnumConstants())
            System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
    }

    // Enum 형을 테스트 하기 위해  한정적 와일드 카드 자료형을 사용
    private static void test2(Collection<? extends Operation> opSet, double x,
            double y) {
        for (Operation op : opSet)
            System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
    }
}
{% endhighlight %}
- _<T extends Enum<T> & Operation> void test(Class<T> opSet, double x, double y)_
    - 자료형의 class 리터럴을 사용, class 리터럴은 한정적 자료형 토큰 역활을 한다.
    - **<T extends Enum<T> & Operation>** : Class 객체가 나타내는 자료형이 enum 자료형인 동시에 Opertion 하위 자료형이 되어야한다. (Enum 자체는 상속이 안되고 같은 interface 를 상속하는 방식이므로 이런 한정적 방법이 필요함)
- _void test2(Collection<? extends Operation> opSet, double x, double y)_
    - EnumSet 이나 EnumMap 을 사용할수 있는 유연성이 가능하다.
    - **하지만 한정적 와일드 카드 자료형 특성상 특정 operation을 직접 수행할수는 없다.**, 실제로 연산을 수행할수 있으르면 한정적 자료형 토큰으로 사용하자!

### ITEM35 : 작명 패턴 대신 어노테이션을 사용하라
- 자바 1.5 이전에는(어노테이션이 없을때) 도구나 프레임워크가 특별히 취급해야 되면 작명패턴을 사용했다
    - JUnit 은 테스트 이름을 test 로 시작하게 했다.
    - 이름을 잘못 입력하면 문제가 발생하거나 무시해서 문제가 생김..
    - 프로그램 요소에 인자를 전달할 방법이없다. 

- 주석 타입 정의 

{% highlight java linenos %}
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

// 어노테이션 사용
// 메타 주석
@Retention(RetentionPolicy.RUNTIME) // Test 주석이 런타임시에 존속되어야 한다
@Target(ElementType.METHOD) // 메소드 선언시에만 적합하다.
public @interface Test {
}
{% endhighlight %}
- 메타 주석 : 주석 타입 선언에 나온 주석

- 특정 예외를 발생 시키는 경우에 한해서 테스트가 성공하도록 만들기

{% highlight java linenos %}
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD) 
public @interface ExceptionTest {
    Class<? extends Exception> value(); // 한개의 타입만 받는다.
    Class<? extends Exception>[] value(); // 여러개의 타입을 받는다.
    /*
    Class<? extends Exception>[] excTypes = m.getAnnotation(
                            ExceptionTest.class).value(); // 처럼 사용
    */
}
{% endhighlight %}
- _Class<? extends Exception>_ 
    - Exception 예외 클래스에서 상속받은 어떤 클래스의 class 객체
    - 이주석 사용자가 어떤 예외 타입을 지정해도 된다!(바운드 타입 토큰)

{% highlight java linenos %}
public class Sample2 {
    // 매개변수로 클래스 리터럴을 받는 주석!
    @ExceptionTest(ArithmeticException.class)
    public static void m1() {
        // 예외 발생하므로 성공하는 테스트
        int i = 0;
        i = i / i;
    }

    @ExceptionTest(ArithmeticException.class)
    public static void m2() { 
        // 잘못된 Exception 으로 실패하는 테스트
        int[] a = new int[0];
        int i = a[1];
    }

    @ExceptionTest(ArithmeticException.class)
    public static void m3() {
    } // 예외가 발생 안하므로 실패하는 테스트

    @ExceptionTest({ IndexOutOfBoundsException.class,
            NullPointerException.class })
    public static void doublyBad() {
        List<String> list = new ArrayList<String>();

        // IndexOutOfBoundsException or NullPointerException 에러 발생
        list.addAll(5, null);
    }
}
{% endhighlight %}

- Test Annotation 처리

{% highlight java linenos %}
public class RunTests {
    public static void main(String[] args) throws Exception {
        int tests = 0;
        int passed = 0;
        Class testClass = Class.forName(args[0]);
        for (Method m : testClass.getDeclaredMethods()) {
            // Test Annotation 이 적용된것만 테스트!
            if (m.isAnnotationPresent(Test.class)) {
                tests++;
                try {
                    m.invoke(null);
                    passed++;
                } catch (InvocationTargetException wrappedExc) {
                    Throwable exc = wrappedExc.getCause();
                    System.out.println(m + " failed: " + exc);
                } catch (Exception exc) {
                    System.out.println("INVALID @Test: " + m);
                }
            }
            
            // 만약에 Exception Annotation 이라면
            if (m.isAnnotationPresent(ExceptionTest.class)) {
                tests++;
                try {
                    m.invoke(null);
                    System.out.printf("Test %s failed: no exception%n", m);
                } catch (Throwable wrappedExc) {
                    Throwable exc = wrappedExc.getCause();
                    
                    // 가능한 예외 목록을 처리
                    Class<? extends Exception>[] excTypes = m.getAnnotation(
                            ExceptionTest.class).value();
                    int oldPassed = passed;
                    // 여러개의 타입을 받는것을 처리
                    for (Class<? extends Exception> excType : excTypes) {
                        if (excType.isInstance(exc)) {
                            passed++;
                            break;
                        }
                    }
                    if (passed == oldPassed)
                        System.out.printf("Test %s failed: %s %n", m, exc);
                }
            }
        }
        System.out.printf("Passed: %d, Failed: %d%n", passed, tests - passed);
    }
}
{% endhighlight %}


### ITEM36 : 어노테이션은 일관되게 사용하라

{% highlight java linenos %}
public class Bigram {
    private final char first;
    private final char second;

    public Bigram(char first, char second) {
        this.first = first;
        this.second = second;
    }

    public boolean equals(Bigram b) {
        return b.first == first && b.second == second;
    }

    public int hashCode() {
        return 31 * first + second;
    }

    public static void main(String[] args) {
        Set<Bigram> s = new HashSet<Bigram>();
        for (int i = 0; i < 10; i++)
            for (char ch = 'a'; ch <= 'z'; ch++)
                s.add(new Bigram(ch, ch));
        System.out.println(s.size());
    }
}
{% endhighlight %}

- 26 개의 바이그램을 10번 반복해서 Set 에 추가함! 정상적이라면 26 을 출력해야 한다! 하지만 모든 오브젝트가 다르게 인식되어서 260개가 출력됨
    - Bigram 클래스 개발자는 equals 를 오버라이딩을 하려 했다 하지만 _eqauls(Object b)_ 가 되어야 하는데 잘못 오버라이딩 해주었다.
    - _@Override_ 주석을 eauls 함수에 넣어주면 컴파일 에러가 발생해서 실수를 방지할수 있다.

### ITEM37 : 자료형을 정의할 때 표식 인터페이스를 사용하라
- 표시 인터페이스(marker interface)는 메소드 선언은 전혀없으면서 클래스가 그 인터페이스를 구현하는지만 나타내는(표시하는) 인터페이스이다.
    - ex) _Serializable_
- 표시 인터페이스는 주석에 비해 2가지 장점이 있다.
    - 표시된 클래스의 인스턴스에 의해 구현되는 타입을 정의한다. 타입이 있기때문에 주석이 런타입에서 에러를 잡을수 있을것을 컴파일 시점에서 할수 있다.
        - _Serialzable_ 의 _ObjectOutputStream.write(Object)_ 함수는 _Serialzable_ 을 메소드 인자 타입으로 이용하지 않고 _Object_ 를 이용했다. 따라서 컴파일 시점이 아닌 런타임에서 오류가 나도록 잘못 설계가 되어있다.
    - 더 정확한 목표를 가실수 있다는 장점이있다. 주석타입이 어떤 목표로 선언된다면 그 주석은 어떤 클래스나 인터페이스에도 적용할수 있게 된다. 표시 인터페이스를 정의하면 그것을 적용 가능한 인터페이스로 확장할수 있고 모든 표시 타입들도 그적용 가능한 인터페이스의 서브 타입이 되도록 할수 있다.
    
- _Set_ 인터페이스는 **제한적 표시 인터페이스** 이다. 
___
- 주석이 표시 인터페이스에 비해 갖는 장점
    - 하나 이상의 주석 타입 요소들을 추가함으로써 더 많은 정보를 주석타입에 추가할수 있다!

- 클래스나 인터페이스가 아닌 다른 포로그램 요소에 적용할 때는 주석을 사용해야 한다.
- 클래스와 인터페이스에만 적용하되 표시자를 갖는 개체만을 인자로 받는 메소드를 하나 이상작성하지 않는다면 그표시자를 영원히 특정 인터페이스의 요소에만 제한해서 사용할 것인지 고려해 봐야한다. 그렇다면 인터페이스의 서브 인터페이스로 표시인터페이스를 정의하는 것이 좋다

- 정의할 타입과 연관된 어떤 새로운 메소드도 갖지 않는 타입을 정의하고자 한다면 표시 인터페이스
- 클래스와 인터페이스가 아닌 다른 프로그램 요소를 표시한다면, 향후에 더많은 정볼르 표시자에 추가할 가능성이 있다면 표시 주석!

