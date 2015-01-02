---
layout: post
title: "WWDC2012 Modern Objective C"
description: ""
category: Mobile
tags: [Objective-C]
---

![WWDC2012LOGO](http://www.blogcdn.com/www.tuaw.com/media/2012/04/wwdc2012.jpg)

## Modern Objective-C
얼마전 wwdc2012 가 있었다~ 모두가 아이폰 소식과 뉴아이패드등에 관심을 가졌지만 어떻케 보면 개발자들이 가장 관심 있게 지켜 본것 Objective-C 에 새로운 변화다!!
ARC 에 등장 때 부터 Objective-C 에 진화가 시작 되는 구나 했는데 이제 그 모습이 점점더 새로워 지고 있다!
이번에 새로 바뀐 Objective-C 의 모습을 조사해 보았다!

## PreView

> 먼저 재밌는 점은 2007년 45 위에 있었던 Objective-C 가 현재 4위로 랭크 되어있다. 무슨 기준으로
> 정했는지는 모르지만 짐작해 보건데 가장 많은 디바이스 위에 올라가 있는 언어 순위가 아닐까 짐작해본다!
Modern Objective-C 에 슬로건은
Simpler ,Safter through automation 이다.
간단하게 작성하고 자동화를 시켜 보다 오류를 줄 일 수 있게 하겠다는 것이다. ARC 의 등장도 이 맥락과 같이한다고 할 수 있곘다. 자동으로 메모리 관리가 되니 개발자가 신경 쓸 부분이 상당히 줄어 든다.
ARC 는 Garbage Collection 과 다르게 컴파일 단에서 알아서 코드를 수정해주는 것으로 성능 면에서도 문제가 없다.
추가적으로 이번 업데이트된 Objective-C 를 사용하기 위해서는 XCode 버젼 4.4 이상을 사용하면 된다.

## 변화된 사항들
**Enum 형 변수의 변화**<br>
Mac OS X v10.5 이전에는 enum 형 변수값의 타입을 가늠할수 없었다.  int 형일지 uint형일지 가늠 할 수 없이 그냥 사용했었다. 그후 v10.5 이후에는
typedef NSUInteger NSNumberFormatterStyle 과 같이 typedef 형을 사용해 타입명을 정해주었다.
하지만 이렇게 사용 할 경우 enum형 변수값과 타입간의 정규적인 관계가 명확하지 않았다. 따라서 이번에 이런점을 보안해서
{% highlight objc linenos %}
typedef enum NSNumberFormatterStyle : NSUInteger {

    NSNumberFormatterNoStyle,

    NSNumberFormatterDecimalStyle,

    NSNumberFormatterCurrencyStyle,

    NSNumberFormatterPercentStyle,

    NSNumberFormatterScientificStyle,

    NSNumberFormatterSpellOutStyle

} NSNumberFormatterStyle;
{% endhighlight %}
이런식으로 변경 되었다. 이렇게 할경우 각 enum 형 타입간의 check 가 가능해지고 변수 타입도 명확해 진다.
기존 XCode 를 사용할경우 Enum 형 상수값을 Code Snippets 할 경우 타입명이 anonymous 로 나오지만 XCode 4.4 이후에 할 경우 정확한 타입명을 확인 할 수 있다.
또한 서로 다른 enum 형을 대입 할 경우 warning 메세지가 나오고 switch 문에서도 모든 값을 체크 하도록 warning 메세지를 출력해준다.

**Property Synthesis 변화** <br>
기존에 @implementation 안에 멤버 변수를 선언해주지 않아도
@property 와 @synthesize 를 입력해줄 경우 자동으로 멤버변수를 만들어 준다는 것을 알고 있을 것이다.
또한 @synthesize name = _name 을 통해서 멤버 변수로 _name 을 지정 할 수 있게 해주었다.
이번에는 이마져 생략 가능하게 만들었다.
@property (strong) 만 선언해준다면 @synthesize 를 쓰지 않아도 자동으로 생성해준다.
하지만 여기서 의문점이 생긴다. 멤버 변수로 어떤 값을 사용 할 것인가 이다. 기존에 애플에서 _name 식으로 멤버 변수를 사용 하는것을 추천하지 않았지만 이번에 업데이트를 통해서 자동으로 _변수명을 사용하도록 하고 있다.
@propery (strong) NSString* name 을 선언해줄 경우 자동으로 _name 의 멤버변수를 생성해준다!
( 단 CoreData 관련해서는 @dynamic 을 그대로 사용해주어야 한다.)

**Literal** <br>
NSNumber  와 NSArray NSDictionary 를 보다 쉽게 선언하고 축약 할수 있게 되었다.<br>
*NSNumber*
{% highlight objc linenos %}
NSNumber *value;
value = [NSNumber numberWithChar:'X'];
value = [NSNumber numberWithInt:12345];
value = [NSNumber numberWithUnsignedLong:12345ul];
value = [NSNumber numberWithLongLong:12345ll];
value = [NSNumber numberWithFloat:123.45f];
value = [NSNumber numberWithDouble:123.45];
value = [NSNumber numberWithBool:YES];
//아래 처럼 변화!!
NSNumber *value;
value = @'X';
value = @12345;
value = @12345ul;
value = @12345ll;
value = @123.45f;
value = @123.45;
value = @YES;
{% endhighlight %}
NSArray
{% highlight objc linenos %}
NSArray *array;
array = [NSArray array];
array = [NSArray arrayWithObject:a];
array = [NSArray arrayWithObjects:a, b, c, nil];
id objects[] = { a, b, c };
NSUInteger count = sizeof(objects) / sizeof(id);
array = [NSArray arrayWithObjects:objects count:count];
//아래 처럼 변화!!
NSArray *array;
array = @[];
array = @[ a ];
array = @[ a, b, c ];
array = @[ a, b, c ];
{% endhighlight %}
NSDictionary
{% highlight objc linenos %}
NSDictionary *dict;
dict = [NSDictionary dictionary];
dict = [NSDictionary dictionaryWithObject:o1 forKey:k1];
dict = [NSDictionary dictionaryWithObjectsAndKeys:
                       o1, k1, o2, k2, o3, k3, nil];
id objects[] = { o1, o2, o3 };
id keys[] = { k1, k2, k3 };
NSUInteger count = sizeof(objects) / sizeof(id);
dict = [NSDictionary dictionaryWithObjects:objects
                                   forKeys:keys
                                     count:count];
//아래 처럼 변화!!
NSDictionary *dict;
dict = @{};
dict = @{ k1 : o1 };
dict = @{ k1 : o1, k2 : o2, k3 : o3 };
dict = @{ k1 : o1, k2 : o2, k3 : o3 };
{% endhighlight %}

\[] 연산자 오버라이딩
{% highlight objc linenos %}
@implementation SongList {
  NSMutableArray *_songs;
}
- (Song *)replaceSong:(Song *)newSong atIndex:(NSUInteger)idx {
    Song *oldSong = [_songs objectAtIndex:idx];
    [_songs replaceObjectAtIndex:idx withObject:newSong];
    return oldSong;
}
//아래처럼 변화!!
@implementation SongList {
  NSMutableArray *_songs;
}
- (Song *)replaceSong:(Song *)newSong atIndex:(NSUInteger)idx {
    Song *oldSong = _songs[idx];
    _songs[idx] = newSong;
    return oldSong;
}
{% endhighlight %}

## ARC and Core Foundation

ARC로 메모리 관리가 자동화 됬다고 하지만 Core Foundation 을 사용 할 경우 Objective-C 과 Cocoa( CF Reference) 간의 메모리 관리에 대한  Ownership 을 정해주어야 한다.
ARC를 사용할 경우 Objective-C 의 pointer type (id) 를 C 의 pointer type(void *) 로 casting 할경우 bridge 를 사용해서 casting 해야한다.

\__bridge - Ownership 에 이동이 없다. 즉 ARC에 도움을 받지 않는다.(ARC가 없는 기존과 같은 castring) 이다. 즉 메모리 해제를 원할경우 CFRelease를 사용해 수동으로 메모리 관리를 해주어야 한다.
{% highlight objc linenos %}
void *aPointer = (__bridge void*)someObject;
{% endhighlight %}
someObject 가 메모리에서 drop 될경우 aPointer 는 danglingPointer 가 되므로 주의해야한다.

\__bridge_transfer , \__bridge_retained - ARC 가 자동으로 메모리를 관리한다. 따로 CFRelease를 하지 않아도 된다. <br>
>\__bridge_transfer 는 CF refrence를 Objective-C Object 로 캐스팅 하고
>\__bridge_retained 는 Objective-C Object를 CF refrence 로 캐스팅한다.
{% highlight objc linenos %}
id my_id;
CFStringRef my_cfref;
NSString   *a = (__bridge NSString*)my_cfref;     // Noop cast.
CFStringRef b = (__bridge CFStringRef)my_id;      // Noop cast.
NSString   *c = (__bridge_transfer NSString*)my_cfref; // -1 on the CFRef
CFStringRef d = (__bridge_retained CFStringRef)my_id;  // returned CFRef +1
{% endhighlight %}
CFObject 에서는 NSObject 보다 retain_count 가 +1 된 상태이다. 두 Object 간에 캐스팅을 할경우 해당 retain count 를 맞추어야 한다.


