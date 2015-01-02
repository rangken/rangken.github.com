---
layout: post
title: "복사생성자 대입연산자"
description: "복사생성자란?"
category: C/Java
tags: [C++]
---
## 복사생성자
복사생성자는 객체와 객체가 복사될때 호출 되는 생성자이다. 복사생성자를 따로 선언해 주지 않으면 컴파일러가 자동적으로 복사생성자를 생성한다.<br>
{% highlight c++ linenos %}
class TClass{
	TClass(){
		// 기본생성자
	}
	TClass(const TClass& cls){
		// 복사생성자!
	}
};
{% endhighlight %}
복사생성자는 기본 생성자에 const TClass& 을 인자로 갖는다.
복사를 수행할 클래스가 변경이 되지 않아야 하므로 const 형으로 선언하고
const TClass 로 선언할 경우 반복적으로 계속 복사생성자가 호출 되므로 refrence 형으로 선언하여야 한다. <br>

### 얕은복사 & 깊은 복사
기본 default 복사생성자의 경우에는 얕은 복사를 수행한다.
- 얕은 복사(shallow copy) : 얕은 복사는 단순 값만 복사한다. 동적으로 할당된 메모리는 복사하지 못한다. 얕은 복사를 수행하는 객체가 원래 복사를 수행했던 기존 객체가 메모리에서 사라진다면 에러가 발생할수 있다.
- 깊은 복사(deep copy) : 깊은 복사는 동적으로 할당된 값까지 복사한다.


### 복사생성자가 호출 되는경우
- **기존에 생성된 객체를 이용해서 새로운 객체를 초기화 할경우** <br>
{% highlight c++ linenos %}
TClass cls = tCls;
{% endhighlight %}
- **Call-by-value 방식으로의 호출(pointer 와 reference 형이 아닌) 과정에서 객체를 인자로 전달 할 경우** <br>
{% highlight c++ linenos %}
void func(TClass cls); // 복사생성자 호출
void funcRef(TClsss& cls); // 복사생성자 호출 안됨
void funcPointer(TClsss* pCls); // 복사생성자 호출 안됨
{% endhighlight %}
- **객체를 반환하는데 참조형으로 반환하지 않을 경우** <br>
{% highlight c++ linenos %}
TClass func(){
	TClass cls;
	//..code
	return cls;
}
{% endhighlight %}

## TroubleShooting
{% highlight c++ linenos %}
class TClass{
public:
	TClass(){
		// 그냥 생성자!
		printf("생성자 호출\n");
	}
	TClass(const TClass& cls){
		// 복사생성자! - 멤버변수들의 복사를 수행해야 한다.
		printf("복사생성자 호출\n");
	}
	void print(){
		// 멤버함수
	}
};
class PClass{
public:
	TClass tCls;
	PClass& getTClassRef(){
		// 복사생성자 없이 그대로 cls 복사본을 전달해줌
		return cls;
	}
	PClass getTClass(){
		// 복사한 cls 값을 리턴해줌
		return cls;
	}
};
int main(void){
	TClass cls; // 그냥 생성자만 호출된다.
	TClass cls2 = cls; // 복사생성자 호출됨
	PClass pCls;
	pCls.tCls = cls;

	TClass tCls1 = pCls.getTClassRef(); // 복사 생성자 호출됨
	TClass& tCls2 = pCls.getTClassRef(); // 복사 생성자 호출되지 않는다. 값 그대로의 Tcalss 를 가져온다.
	TClass tCls3 = pCls.getTClass(); // 복사생성자 호출됨
	// tCls1 과 tCls3 는 복사생성자가 호출된 새로운 객체다
	// cls 와 tCls1 과 tCls3와 주소 다르다.
	// cls 는 tCls2 는 주소가 같다.
	mCls.getTClassRef().print();  // 복사생성자가 호출되지 않는다.
	mCls.getTClass().print(); // 복사생성자 호출된다.
	// 위 두 객체의 결과는 다르게 나올수 있다.
	// 복사생성자가 재대로 값을 복사를 못할경우 두 멤버 변수의 값은 다를 수 있다.
	return 0;
}
{% endhighlight %}

## 대입연산자
대입 연산자는 연산자 오버로딩의 결과로써 실행된다. 복사 생성자와 비슷하게 값에 copy를 수행한다.
생성자와 마찬가지로 따로 선언하지 않을 경우 default로 얕은 복사를 하는 대입 연산자를 컴파일러가 자동으로 만든다.
{% highlight c++ linenos %}
class TClass{
public:
	// 기본 대입연산자
	TClass& operator =(const TClass& cls){
		if(this != &cls){
			// 복사수행
		}
		retur *this;
	}
};
{% endhighlight %}
대입 연산자는 TClass& 로 reference 형을 return 한다. 이는 cls = cls2 = cls3; 처럼 연속으로 대입 연산자를 수행가능 하도록 만들기 위함이다. const TClass& cls 으로 선언한 이유는 복사 생성자 처럼 연속으로 복사 생성자가 호출 되지 않도록 하기 위함이다.
{% highlight c++ linenos %}
int main(void){
	TClass cls; // 그냥 생성자만 호출된다.
	TClass cls2 = cls; // 대입 연산자가 호출 되는게 아니라 복사 생성자가 호출됨
	TClass cls3;
	cls3 = cls2; // 대입 연산자가 호출됨
}
{% endhighlight %}

## 유의 할점!
{% highlight c++ linenos %}
TClass(){
public:
	PClass *pCls; // 특정 객체를 가리키는 pointer
};
PClass* testFunction(){
	TClass cls;
	// code;
	return cls.pCls;
}
int main(void){
	TClass tCls;
	PClass* pCls = tCls.testFunction();
	// TClass 가 메모리에서 사라지면서 pCls 는 값을 잃어 버린다.
	return 0;
}
PClass* testFunction(){
	// 복사생성자를 호출해서 그 값을 리턴해주던지
	// PClass& 형을 리턴하는게 좋다!
	return &PClass(cls.pCls);
}
{% endhighlight %}
