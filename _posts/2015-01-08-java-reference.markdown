---
layout: post
title:  "Weak Strong Refernece"
date:   2015-01-08 18:07:40 +0900
categories: Programming
tags: Java C# Obj-C
description: Weak Strong Reference
---


## Weak Strong Reference
- C++ 과 다르게 Java C# 언어를 사용하면 가비지 컬랙션이라는 메모리 수집 자동화를 만나게 된다. 가비지 컬랙션은 Reachablity 라는 개념을 통해 메모리를 관리하므로 Weak Strong Reference 의 개념을 이해해야 가비지 컬랙터가 잘 동작하도록 만들수 있다.

### Strong
- *"keep this in the heap until I don't point to it anymore"*
- 더이상 특정 객체를 참조하지 않을때까지  힙에서 유지함
- ex) 특정 객체(개) 를 여러 사람이 목줄로 지키고 있다. 한사람도 목줄로 개를 지키지 않는다면 개는 도망간다(dealloc 메모리에서 사라진다)

### Weak
- *"keep this as long as someone else points to it strongly"*
- 다른 포인터가 특정 객체를 참조하고 있을때까지만 유지함
- ex) 특정 객체(개) 가 목줄로 묶여있다(강하게 참조되어있음). 아이들이 그냥 개를 가리키고 있다. 여러명의 아이가 개를 가리키고 있지만 만약 개의 목줄이 풀린다면(더이상 강하게 참조되어 있지 않다면)  개는 도망간다(메모리에서 삭제됨)

### Case1 (Objective-C)
{% highlight objective-c linenos %}
__strong id strongObject = <some_object>;
__weak id weakObject = strongObject;
{% endhighlight %}

> 1. strongOjbect = nil 을 한다면
strongObject 는 더이상 어떤 객체도 참조 하지 않는다. 따라서 <some_object> 는 메모리에서 사라진다.
weakObject 다른 포인트거 특정 객체를 참조하지 않으므로(아무도 특정객체를 참조 x) <some_object> 는 메모리에서 사라진다.
2. weakObject = nil 을 한다면
 strongObject 는 <some_object> 를 참조하고 있으므로 메모리에서 사라지지 않는다.
 weakObject 는 <some_object>를 더이상 참조 하지 않는다. weakObject 만 nil 이된다.

### Case2 (C#)
{% highlight csharp linenos %}
Fruit apple = new Fruit("Apple");
Fruit orange = new Fruit("Orange");
    
Fruit fruit1 = apple;   // strong reference
WeakReference fruit2 = new WeakReference(orange); // weak reference
Fruit target;
    
target = fruit2.Target as Fruit;

apple = null; // 강한 참조한것을 null 해도 Fruit fruit1 = apple; 해서 이미 강한 참조하고 있으므로 메모리에서 사라질수없다
orange = null; // WeakReference fruit2 = new Weak.. 에서 참조하고 있지만 약하게 참조하므로 
//orange = null; 되면 fruit2는 메모리 에서 사라진것을 가리킴

// 메모리 정리전 fruit1 - Apple , target - Orange
System.GC.Collect(0, GCCollectionMode.Forced);
System.GC.WaitForFullGCComplete(); // 가비지 정리
// 메모리 정리후 fruit - Apple(안사라짐) target - null(약한참조한건 사라짐)
{% endhighlight %}


### TODO
- WeakHashMap : ReferenceQueue
- WeakReference 쓰는 경우 예제
