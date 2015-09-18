---
layout: post
title:  "Effective Java 5장 제네릭"
date:   2015-01-07 18:07:40 +0900
categories: Java
tags: Java effective-java
description: 제네릭 (23~29)
---


## 제네릭 (23~29)
- 자바 1.5 부터  가능
    - 그전에는 컬랙션에서 객체를 읽어낼때마다 형변환을 해야했다.
    - 컬랙션에 이상한거 넣어서 에러나고 그랬음(컴파일 타임에서 에러를 검출 못했다)

### ITEM23 : 새코드에는 무인지 제네릭 자료형을 사용하지 마라
- 용어
    - 형인자(type parameter) :
    - 형인자 자료형(parameterized type) : List \<String\>
    - 실형인자(actual type parameter) : String
    - 제네릭 자료형(generic type) : List\<E\>
    - 형식 형인자(Formal Type Parameter) : E
    - 무인자 자료형(raw type) : List
    - 한정적 형인자(bunded type parameter) : \<E extends Number\>
    - 재귀적 형 한정(recursive type bound) : \<T extends Comparable\<T\>\>
    - 비한정적 와일드카드 자료형(unbounded wildcard type) : List<?>
    - 한정적 와일드카드 자료형(bounded wildcard type) : List<? extends Number>
    - 제네릭 메서드(generic method) : static <E> List<E> asList(E[] a)
    - 자료형 토큰(type token) : String.class
    -
- 제네릭(Generic) : 형인자가 포함된 클래스나 인터페이스
    - List 인터페이스는 List\<E\> 이렇게 부르는게 맞다
    - List\<String\> : 스트링 인터페이스

- 무인자 자료형은 사용하면 안된다. 컴파일 타임에서 타입 안정성을 보장할수 없다. (ClassCastException 발생 함)
    - size 무인자 자료형은 아직도 지원하긴한다. 무인자 자료형을 인자로 받는 메서드에 형인자 자료형 객체를 전달할수 있어야 하고 반대도 가능해야 한다. 이진 호환성을 지원하기 위해 어쩔수 없이 존재한다.

- 형인자 자료형을 사용하면 엉뚱한 자료형의 객체를 넣는 코드를 컴파일 할때 무엇이 잘못인지 알수 있다. 컬랙션에서 원소를 꺼낼떄 형변환을 하지않아도 된다. (컴파일러가 알아서 해줌)

- List vs List\<Object\>
    - List 는 완전히 형검사 절차를 생략한것이고 List\<Object\> 는 형검사를 진행한다
    - List 에는 String 을 넣을수 있지만 List\<Object\> 에는 넣을수 없다.
    - List 에는 메서드에 List\<String\> 을 전달 가능하지만 List\<Object\> 는 불가능하다
    - List\<String\> 은 List 의 하위자료형(subtype) 이지만 **List\<Object\>의 하위 자료형은 아니다**
    - List 와 같은 무인자 자료형을 사용하면 형 안전성을 잃게 되지만 List\<Object\> 와 같은 형인자 자료형은 형안전성이 있다.

- 실행 도중 오류를 일으키는 무인자 자료형(List)

{% highlight java linenos %}
public static void main(String[] args){
    List<String strings = new ArrayList<Object>();
    unsafeAdd(strings, new Ineger(42));
    String s = strings.get(0); // ClassCastException 발생!!!!
}
// 무인자 자료형에 인자로 보낼수는 있음
private static void unsafeAdd(List list, Object o){
    list.add(0); // 경고 발생 unchecked call to add(E) in raw type List
}
// unsafeAdd(List<Object>... 로 바꾸어야 한다)
{% endhighlight %}

- 비한정적 와일드 카드 자료형
    - 제네릭 자료형을 쓰고 싶으나 실제형인자가 무엇인지는 모르거나 신경쓰고 싶지 않을때는 형인자로 **?** 를 쓰면된다.
    - Set\<?\> : 가장 일반적인 형인자 Set 자료형

{% highlight java linenos %}
//static int numElementsInCommon(Set s1, Set s2) { // 무인자 사용하면안된다
static int numElementsInCommon(Set<?> s1, Set<?> s2) { // 비한정적 와일드 카드 자료형
    int result = 0;
    for(Object o1 : s1){
        if(s2.contains(01)){
            result++;
        }
    }
    return resultt;
}
{% endhighlight %}
- 와일드 카드 자료형은 안전, 무인자자료형은 안전하지 않다.
- 무인자 자료형에는 아무거나 넣을수 있어서 자료형 불변식이 쉽게 깨진다. Collection\<?\> 에는 null 이외에 어떤원소도 넣을수가 없다. 무언가 넣을려고 하면 컴파일 오류가 난다. 어떤 자료형 객체를 꺼낼수 있는지도 알수없다.

- 무인자 자료형을 그래도 써도 되는경우
    - 클래스 리터럴(class literal)
        - 자바 표준에서 클래스 리터럴에는 형인자 자료형을 쓸수 없다.(배열 자료형이나 기본 자료형은 쓸수있다)
        - List.class, String[].class int.class 는 가능하다
        - List\<String\>.class 나 List\<?\>.class 는 사용할수가 없다.
    - instanceOf 연산자 사용규칙
        - 제네릭 자료형 정보는 프로그램이 실행될때는 지워지므로 instanceOf 연산자는 형인자 자료형에 적용할수가없다. 비한정적 와일드 카드 자료형은 가능하다. 하지만 코드만 지저분해질뿐 굳이 쓸이유가없다.

{% highlight java linenos %}
if (o instanceof Set){ // 무인자 자료형
    Set<?> m = (Set<?>) 0; // 와일드 카드 자료형
}
{% endhighlight %}

### ITEM24 : 무검검 경고(unchecked warning)를 제거하라
- 제네릭을 사용해서 프로그램을 작성하면 컴파일 경고 메세지를 많이 보게 됨
    - unchecked 캐스트 경고
    - unchecked 메소드 호출 경고
    - unchecked 제네릭 배열 생성 경고
    - unchecked 변환 경고
- unchecked 예외를 무시하면 ClassCastException 가 생길수 있으므로 조심한다.
- @SuppressWarnings("unchecked") 주석을 사용해서 경고 메세지를 안나타나게 억제할수 있다.
    - 다양한 범위로 사용 가능하다. 가급적 제일 작은 범위로 사용하도록 해야한다.
    - 가능한 주석으로 SuppressWarnings을 사용하는 이유를 남겨라!

{% highlight java linenos %}
// ArrayList 의 toArray 함수
public <T> T[] toArray(T[] a){
    if (a.length < size){
        // unchecked cast (Object[] , required: T[])
        @SuppressWarnings("unchecked") T[] result = (T[])Arrays.CopyOf(elements, size, a.getClass());
        return result;
        // return 문제  @SuppresseWarnings 를 사용할수 없다.
        //return (T[])Arrays.CopyOf(elements, size, a.getClass());
    }
    System.arraycopy(elements, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}
{% endhighlight %}

### ITEM25 : 배열 대신 리스트를 써라
- 배열(Array) vs 제네릭 타입
    - 배열 공변(covariant)이다. Sub 이 Super 의 서브타입이라면 Sub[] 은 Super[]의 서브 타입이다.
    - 제네릭은 불변(invariant)이다. Type1 Type2 List\<Type1\> List\<Type2\> 의 서브타입도 슈퍼타입도 아니다.

    {% highlight java linenos %}
    // 런타임에서 에러 발생
    Object[] objectArray = new Long[1];
    objectArray[0] = "I don't fit in"; // ArrayStoreException 예외 발생
    // 컴파일 에러! 컴파일 에러가 더 안전하고 좋은것!
    List<Object> ol = new ArrayList<Long>(); // 호환이 안되는 타입이다!
    ol.add("I don't fit in");
    {% endhighlight %}

- 배열은 구체적(reified) : 자신의 요소타입을 런타임시에 알고 지키게 한다. String 객체를 Long 배열에 저장 하려고 하면 런타임에서 ArrayStoreException 예외 발생
- 제네릭은 소거자(Erasure) 에 의해 구현됨. 컴파일 시에만 자신의 타입 제약을 지키게 하고 런타임 시에는 자신의 요소타입 정보를 무시(소거) 한다.

- new List\<E\>[], new List\<Sting\>[] , new E[] 와 같은 배열 생성식은 불가
    - 제네릭 배열 생성은 불가
- E, List\<E\>, List\<String\> 과 같은 타입들을 비구체화(nonreifiable) 타입 이라고한다.
    - 비구체화 타입이란 컴파일 시보다 런타임 시에 더 적은 정보를 갖는
    - 비구체화 타입은 배열 생성이 불가능
    - List\<?\>, Map\<?,?\> 와 같은 언바운드 와일드 카드 타입은 구체화 타입이다. 따라서 배열 생성 하는건 적법함
    - 따라서 제네릭 타입을 가변인자를 갖는 메소드와 함께 사용 불가능,가변인자는 내부적으로 배열이 생성되는 구조이기 때문
- 제네릭 배열 생성 에러가 발생하면 E[] 보다는 List<E> 를 사용하는것이 좋다
- 다중 스레드간에 동기화에 제네릭 배열이 좋다.

{% highlight java linenos %}
// 제네릭을 사용하지 않으면서 동시성에도 결함이 없다.
// synchronized(list) 로 전체를 묶는 방법이 있지만 동기화된 코드에서는 외계인(alien) 메소드(apply) 를 호출 하면안된다.
// 따라서 lock 이걸로는 toArray() 를 사용해서 문제를 해결했다.
static Object reduce(List list, Functon f, Object initVal){
    Object[] snapshot = list.toArray(); // 내부적으로 List 에 lock 이 걸림
    Object result = initVal;
    for(Object o : snapshot)
        result = f.apply(result, o);
    return result;
}

static <E> E reduce(List<E> list, Functon<E> f, E initVal){
    // 타입 안정성이 보장되지 않는다. 런타임시에 E 가 무슨 타입이 될지 컴파일러가 알지 못한다.
    // ClassCastException 예외가 발생할수 있다.
    // 컴파일 시에는 String[] Integer[] 등 아무거나 될수있지만 런타임에서는 Object[] 이므로 위험
    // E[] 로 캐스팅 하는건 특별한 상황에서만 고려 되야함
    E[] snapshot = (E[]) list.toArray();
    E result = initVal;
    for(E o : snapshot)
        result = f.apply(result, o);
    return result;
}


static <E> E reduce(List<E> list, Functon<E> f, E initVal){
    E[] snapshot;
    synchronized(list) {
        // toArray 함수와는 다르게 락이 걸리지 않으므로 synchronized 함수로 변경해야한다.
        snapshot = new ArrayList<E>(list); // 배열보다는 ArrayList<E>
    }
    E result = initVal;
    for(E o : snapshot)
        result = f.apply(result, o);
    return result;
}
{% endhighlight %}

### ITEM26 : 가능하면 제네릭 자료형으로 만들 것
{% highlight java linenos %}
// Object 객체 기반의 컬랙션
public class Stack{
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    public Stack(){
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    public void push(Object e){...}
    public Object pop(){
        if(size==0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // 쓸모없는 참조 제거
        return result;
    }
}

// 제네릭 적용1 - 캐스팅 이용
public class Stack<E>{
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    // E[] 로 캐스팅 하므로 warning 이 발생한다.
    // elements 는 private 로써 밖에서는 사용이 안되므로 해당 캐스팅만 문제없으면 외부적으로도 문제 없으므로, 아래처럼 캐스팅 하는것은 문제가 없다.
    @SuppressWarnings("unchecked")
    public Stack(){
        // 제네릭 배열 생성, 컴파일 에러 발생!  비구체화 타입을 저장하는 배열은 생성할수 없다.
        //elements = new E[DEFAULT_INITIAL_CAPACITY];
        elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
    }
    public void push(Object e){...}
    public Object pop(){
        if(size==0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // 쓸모없는 참조 제거
        return result;
    }
}

// 제네릭 적용2 - elements 타입자체를 변경
public class Stack<E>{
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack(){
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    public void push(E e){...}
    public E pop(){
        if(size==0)
            throw new EmptyStackException();

        @SuppressWarnings("unchecked")
        E result = (E)elements[--size];
        elements[size] = null; // 쓸모없는 참조 제거
        return result;
    }
}
{% endhighlight %}

- 배열 타입에 대한 unchecked 캐스트 경고를 억제하는게 더 위험하므로 적용2 가 더 좋은 방법 일 수 있다.
- 하지만 적용2는  elements 를 사용하는 모든 부분에 캐스팅을 해야하고 @SupressWarnings 를 적용해야 되므로 더 많은일을 해줘야한다.
- Stack 클래스에서는 내부적으로 배열을 사용했다. 배열보다는 List 를 사용하라고 강조했지만 자바 언어 자체는 List 를 지원하지 않으므로 ArrayList 와 같은 일부 제네릭 타입은 내부적으로 배열을 사용한다. HashMap 은 성능 향상을 목적으로 배열을 사용하기도 한다.
- 제네릭 타입은 매개변수가 갖는 제약이 전혀없다. Stack\<Object\> Stack\<int[]\> Stack\<List\<String\>\> 등 여러 형태가 가능하다.
    - \<E extends Delayed\> 같은 바운드 타입 배개변수(bounded type parameter)는 허용가능한 값을 제한할수 있다.
- 하지만 Stack\<int\> Stack\<double\> 같은 기본형은 불가능하다. 박스형 기본형 Integer Double 클래스를 사용하는것이 좋다.


### ITEM27 : 가능하면 제네릭 메서드로 만들 것
- Collections 클래스의 모든 알고리즘 메소드들(binarySearch sort)는 제네릭화 되어있다.

{% highlight java linenos %}
// 제네릭이 적용안된 메소드
public static Set union(Set s1, Set s2){
    // Warning! HashSet(Collection<? extends E>)
    Set result = new HashSet(s1);
    // Warning! result.addAll(Collection<? extends E>)
    result.addAll(s2);
    return result;
}
// 제네릭 적용 메소드
public static <E> Set<E> union(Set<E> s1, Set<E> s2){
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
}
{% endhighlight %}

- 타입매개변수를 선언하는 타입 매개변수 목록을 메소드의 접근 지시자와 반환 타입 사이에 둔다. \<E\>
- 반환 타입 Set\<E\>

- 제네릭 메소드로 중복 제거

{% highlight java linenos %}
Map<String, List<String>> anagram = new HashMap<String, List<String>>(); // 중복된 타입들

// 제네릭 static 팩토리 메소드
public static <K, V> HashMap<K,V> newHashMap{
    return new HashMap<K,V>();
}
Map<String, List<String>> anagrams = newHashMap();
{% endhighlight %}
- 이런 제네릭 메소드가 기본으로 JDK 있으면 좋지만 없음.

- 제네릭 싱글톤 팩토리

{% highlight java linenos %}
public interface UnaryFunction<T>{
    T apply(T arg);
}
// 불변적이지만 여러타입에대한 적합한 객체 생성
private static UnaryFunction<Object> IDENTIFY_FUNCTION = new UnaryFunction<Object>(){
    public Object apply(Object arg) { return arg;}
}

// 상태값이 없고 언바운드 타입 매개변수를 갖는다.
// 따라서 모든 타입에서 하나의 인스턴스를 공유해도 안전
@SuppressWarnings("unchecked")
public static <T> UnaryFunction<T> identityFunction(){
    return (UnaryFunction<T>)IDENTIFY_FUNCTION;
}
// 사용
UnaryFunction<String> sameString = identityFunction();
UnaryFunction<Number> sameNumber = identifyFunction();
{% endhighlight %}
- 재귀적 타입 바운드
     - Comparable 인터페이스와 가장 많이 사용

{% highlight java linenos %}
public static <T extends Comparable<T>>
{% endhighlight %}
- 자신과 비교될수 있는 모든 타입 T

- 캐스팅 없이 메소드를 사용할수 있다는것은 메소드를 제네릭 하게 만들었다는 의미

### ITEM28 : 한정적 와일드카드를 써서 API 유연성을 높여라
- 매개변수화 타입은 불변 타입
- 불변(invariant)!! Type1 Type2 에 대해서 List\<Tyep1\> 은 List\<Type2\> 의 서브타입도 아니고 슈퍼 타입도 아님
- List\<Object\> 에는 아무거나 저장 가능하지만 List\<String\> 에는 스트링만 저장 가능

- 스택 pushAll pop 메소드

{% highlight java linenos %}
// 와일드 카드 타입을 사용하지 않는 pushAll - 불충분함!
public void pushAll(Iterable<E> src){
    for(E e: src)
        push(e);
}
Stack<Number> numberStack = new Stack<Number>();
Iterable<Integer> integers = ..;
// 에러 메세지 pushAll(Iterable<Number>) in Stack<Number>
// 불변형(상속관계아님) 이기 때문에 Integer iterable 은들어갈수없다.
numberStack.pushAll(integers);

// 와일드 카드 사용하지 않은  popAll 메소드 - 불충분함!
public void popAll(Collection<E> dst){
    while(!isEmpty())
        dst.add(pop());
}
Stack<Number> numberStack = new ..
Collection<Object> objects = ...;
// 컴파일 에러! Collection<Object> 는 Collection<Number> 의 서브 타입이 아니다!
numberStack.popAll(objects);
{% endhighlight %}

- 바운드 와일드 카드 타입(bounded wildcard type)
    - pushAll 에는 E의 Iterable 이 아닌 E의 어떤 서브타입의 Iterable 이 되어야 한다.

{% highlight java linenos %}
// 와일드 카드 타입 pushAll 은 E 타입을 생산하므로 extends
public void pushAll(Iterable<? extends E> src){...}
{% endhighlight %}

- popAll 메소드의 인자 타입은 E타입을 저장하는 Collection 이 아닌 E의 어떤 수퍼 타입을 저장하는 Collection이 되어야 한다.

{% highlight java linenos %}
// 와일드 카드타입 popAll 은 E 타입을 소비하므로 super
public void popAll(Collection<? super E> dst){...}
{% endhighlight %}

- **유연성을 극대화 하려면 메소드 인자에 와일드 카드 타입을 사용하자.**
- PECS : Producer->Extends, Consumer->Super
    - T가 생산자를 나타내면 \<? extedns T\>
    - T가 소비자를 나타내면 \<? super T\>

{% highlight java linenos %}
static <E> E reduce(List<E> list, Function<E> f, E initVal)
// E가 생산자 역활을 하는 와일드 카드 타입 변수
statiac <E> E reduce(List<? extends E> list, Function<E> f, E initVal);
{% endhighlight %}
- 와일드 카드를 쓰므로 인해 List<Integer> 와 Function<Number> 를 사용해서 reduce 호출 가능하다!

- 반환 타입에는 와일드 카드 타입을 사용하지 말자
    - 유연성 보다는 클라이언트 코드에서 와일드 카드 타입을 사용해야 하는 문제가 생긴다.
    - 클라이언트 코드에서 와일드 카드 타입 때문에 고민해야 된다면 그 클래스의 API 가 잘못된거다.
- 명시적 타입 매개변수

{% highlight java linenos %}
public static <E> Set<E> union(Set<? extends E> s1, SET<? extends E> s2){...}
Set<Integer> integers = ..
Set<Dobule> doubles = ...
// 컴파일 에러! 타입 추론을 할수가없다.
Set<Number> numbers = union(integers, doubles);
Set<Number> numbers = Union.<Number>union(integers, doubles);
{% endhighlight %}
- Comparable\<T\> 는  T 인스턴스를 소비한다. Comparable\<? super T\> 로 교체
    - Comparator\<T\> 도 마찬가지

{% highlight java linenos %}
public static <T extends Comparable<? super T>> T max(List<? extends T> list){
    // 컴파일 에러! Iterator<? extends T> 리턴한다.
    Iterable<T> i = list.iterator();
    Iterable<? extends T> i = list.iterator();
    T result = i.next();
    while(i.hasNext()){
        T t = i.next();
        if(t.compareTo(result) >) result = t;
    }
    return result;
}
{% endhighlight %}

- 언바운드 타입 매개변수 vs 언바운드 와일드 카드

{% highlight java linenos %}
// 언바운드 타입 매개변수
public static <E> void swap(List<E> list, int i, intj);
// 언바운드 와일드 카드
public static void swap(List<?> list, int i, int j);
{% endhighlight %}
- public API 라면 언바운드 타입 매개변수를 사용하는것이 좋다.
- **메소드 선언부 타입 매개변수가 한번만 나타나면 그것을 와일드 카드로 바꾸면 된다**

{% highlight java linenos %}
public static void swap(List<?> list, int i, int j){
    // 컴파일 에러! List<?> 이므로 list 에는 null 제외한 어떤값도 추가할수 없다.
    list.set(i, list.set(j, list.get(i)));
}

public static void swap(List<?> list, int i, int j){
    swapHelper(list, i, j);
}
// private 지원 메소드
private static <E> void swapHelper(List<E> list, int i, int j){
    list.set(i, list.set(j, list.get(i))));
}

{% endhighlight %}

### ITEM29 : 형 안전 다형성 컨테이너를 쓰면 어떨지 따져보라
- 컨테이너에 제네릭을 사용하면 컨테이너 당 사용 가능한 타입 매개변수의 숫자가 제한된다. 컨테이너에 들어가는 타입이 결정되어있기 때문에.
    - 컨테이너 자체보다는 요소의 키에 타입매개변수를 두면 그런 제약을 극복할수 있고 서로 다른 타입의 요소가 저장 될수 있다. 이런 컨테이너를 혼성 컨테이너라고 부른다.
- 제네릭은 Set Map 그리고 ThreadLocal AtomicReference 같은 단일요소 저장 컨테이너에도 쓰임
- 제네틱 타입 시스템을 키(Class\<T\>)로 사용해서 Map Set을 만듬, 그것을 혼성 컨테이너라고 부름!
    - 클래스 리터럴 타입 : Class\<T\>
    - 컴파일과 런타입 두 시점 모두의 타입정보를 전달될때 그것을 타입토큰(type token)

{% highlight java linenos %}
// 타입 안전이 보장되는 혼성 컨테이너 패턴!
public class Favorite{
    private Map<Class<?>, Object> favorites = new HashMap<Class<?>, Object>();
    public <T> void putFavorite(Class<T> type, T instance){
        if(type == null)
            throw
        favorites.put(type, instance);
    }
    public <T> T getFavorite(Class<T> type){
        // Map 에는 Object 가 들어있지만 T 타입으로 리턴해야한다.
        // 런타임시에 동적으로 cast 하는 cast 함수 Class<T> 정보가 있으므로 가능!
        return type.cast(favorites.get(type));
    }
}
{% endhighlight %}
- Map\<Class\<?\>, Object\> 에서 언바운드 와일드 카드 타입때문에 아무것도 Map 에 넣을수 없다고 생각할수 있지만 키값에 와일드 카드가 붙어 있다. 따라서 모든 키가 서로 다른 매개변수화 타입을 가질수 있다! 예를 들어 Class\<String\>, Class\<Integer\>

- 혼성 컨테이너 문제
    - Class 객체를 원천 타입의 형태로 사용하면 타입 안전이 보장되지 않을수 있다. 해결책은아래!

{% highlight java linenos %}
// put 메소드, 동적 캐스트를 사용해서 런타임 시의 타입 안전을 획득!
public <T> void putFavorite(Class<T> type, T instance){
    favorites.put(type, type,cast(instance);
}
{% endhighlight %}

- 비구체화 타입에 사용 될 수 없다. Favorite 객체를 String 이나 String[] 에는 저장할수 있지만 List\<String\> 은 저장할수 없다.
    - List\<String\> 에 대한 Class 객체를 얻을수 없다. List\<String\>.class 구문 에러
    - 바운드 타입 토큰을 사용하면 해결이 가능하긴하다.

- 바운드 타입 토큰
{% highlight java linenos %}
public <T extends Annotation> T getAnnotation(Class <T> annotationType);
{% endhighlight %}

- annotationType 은 Annotation 타입을 나타내는 바운드 타입 토큰이다.
- 키가 Annotation타입 이고 타입 안전이 보장되는 혼성 컨테이너

{% highlight java linenos %}
static Annotation getAnnotation(AnnotationElement element, String annotationTypeName) {
    Class<?> annitationType = null; // 언바운드 타입 토큰
    try{
        annotationType = Class.forName(annotationTypeName);
    }catch{
        ...
    }
    // asSubClass 메소드를 사용해서 언바운드 타입토큰을 바운드 타입 토큰으로
    return element.getAnnotation(annotationType.asSubClass(Annotation.class));
}
{% endhighlight %}

- Class.forName 은 Class\<?\> 를 리턴함
- Class\<?\> 아입을 Class\<? extends Annotation\> 으로 캐스팅 하기위해 asSubClass 라는 메소드를 사용
