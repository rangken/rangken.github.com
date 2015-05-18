---
layout: post
title: "Ruby Closure"
description: "Ruby Closure - Lambdas Block Proc Method-Object"
category:
tags: [Ruby, Rails]
title: "Ruby Closure(Lambdas Procs Blocks)"
description: ""
category:
tags: []
---
![License](/assets/images/ruby.jpeg)

Ruby는 이름 만큼이나 참 좋은 언어다. 내가 요즘 매우 관심있게 보는 언어중에 하나이기도 하나~ Ruby는 가장 유명한 웹 프레임 워크인 [RubyOnRails](https://github.com/rails/rails) 에 사용되며 맥용 패키지 관리툴인 [Homebrew](https://github.com/mxcl/homebrew) 에서도 사용되고 XCode에 의존적 패키지를 관리하는 툴인 [CocosPods](https://github.com/CocoaPods/CocoaPods) 등등에서 사용된다. 특히 맥을 사용하는 유저에게 많은 영향을 미치고 있는것 같다.

루비가 아무리 인간 친화적인 스트레스 없는 프로그래밍이라고는 하지만 어려운 가끔 어려운 부분이 존재하긴 한다. 그중 하나가 Lambda 나 Procs Block 을 사용한 Closure 프로그래밍을 할 경우다. 위 3개는 비슷하면서도 조금씩 다르다.

## Block
가장 쉽고 흔하게 사용되는 루비스러운 Clouser사용 방법이다.
사용 방법은
{% highlight ruby linenos %}
def function
  puts "function called"
  yield "You are block"
end

fuction do |param|
	puts "block called #{param}"
end
# function called
{% endhighlight %}
블락은 do `|파라메터..| end` 로 구성되고 `yield 파라메터`로 호출될수 있다.
블락의 특성은 다른 함수처럼 이름이 없으며 단순히 yield를 통해 호출된다는 것이다.
하지만 yield가 아니고 Proc를 통해서 호출 하는 방법도 존재한다.
{% highlight ruby linenos %}
def function(&block)
  puts "function called"
  block.call("You are Proc")
  # puts block.class
end

fuction do |param|
	puts "block called #{param}"
end
{% endhighlight %}
위 처럼 해도 똑같은 결과를 얻을 수 있다. 루비 함수인 class를 통해 block의 타입을 출력해보면 Proc 임을 알 수 있다.

>>function 에 파라메터로 `ampersand - &` 가 사용된것을 볼수 있다. 보통 ruby 파라메터는 & 를 붙이지 않지만 블락을 받을 경우 & 가 사용된다. &는 C에서 사용되는 reference형과 의미가 다르다. &의 의미는 루비가 내부적으로 to_proc 라는 함수를 호출 하도록 만들도록 한다.

block 이 Proc 과 같다고 했지만
그 기능적인 내용이 같을뿐이지 내부적으로 Proc의 경우에는 Ruby 의 class 이지만 block 는 클래스가 아니다.
Ruby는 완전한 Object-Oriented 프로그래밍 이기 때문에 코드의 재사용을 하도록 한다. block 의경우에는 저장되지 않으며 재사용 되지 않지만
Proc 의 경우 변수로 저장되며 재사용 된다. 즉 block은 저장되지 않는 Proc 라고 할 수 있다.

## Proc (Procedures)
블락은 매우 쉽고 단간하지만 만약 매우 많은 블락들을 사용하고 그 블락들을 여러번 사용 하려고 한다면 블락들을 변수화 하고 싶을 것이다.
즉 블락을 저장해서 클래스로 만든것이 Proc이다.
{% highlight ruby linenos %}
def function(code)
  puts "function called"
  code.call("You are Proc")
  # puts code.class
end

proc = Proc.new do |param|
	puts "Proc called #{param}"
end

function(proc)
{% endhighlight %}
 Proc 를 사용하는 방법이다. `ampersand & 를 사용하지 않고 그냥 code를 사용해서 call 를 할수 있다` 즉 to_proc를 호출 하지 않다도 proc 변수 자체가 Proc 타입이기 때문이다.
{% highlight ruby linenos %}
def callbacks(procs)
	procs[:starting].call # staring proc 호출
	puts "Still going"
	procs[:finishing].call # finishing proc 호출
end

callbacks(:starting => Proc.new{ puts "Staring"},
	:finishing=>Proc.new{ puts "Finishing"}) # dictionary 형태로 Proc 전달
{% endhighlight %}
위와 같이 2개 이상의 clouser를 사용 할 수도 있다.
Block 과 Proc을 요약하자면

- Block : 메소드를 여러개로 나누면서 서로 상호작용 하도록 만들고 싶다면
- Block : Database migration 처럼 여러 표현을 자동적으로 동작 시키고 싶다면
- Proc : 블락을 여러번 재사용 하고 싶다면
- Proc : 메소드가 여러개 이상 콜백을 사용하려고 한다면

## Lambads
람다는 Ruby에서 Clouser을 구현하기 위해 사용 하는 또 다른 방법은 lambda 이다.
{% highlight ruby linenos %}
def function(code)
  puts "function called"
  code.call("You are Proc")
  # puts code.class # Proc
end

lambda = lambda do |param|
  puts "lambda called #{param}"
end
function(lambda)
{% endhighlight %}
lambda 도 Proc와 마찬가지로 같은 방법으로 사용될수 있다. 그렇다면 lambda 와 Proc 가 다른것은 무엇일까
{% highlight ruby linenos %}
def args(code)
  one, two = 1, 2
  code.call(one, two)
end

args(Proc.new{|a, b, c| puts "Give me a #{a} and a #{b} and a #{c.class}"})
args(lambda{|a, b, c| puts "Give me a #{a} and a #{b} and a #{c.class}"})
# => Give me a 1 and a 2 and a NilClass
# *.rb:8: ArgumentError: wrong number of arguments (2 for 3) (ArgumentError)
{% endhighlight %}
>> lambda 는 함수 인자의 갯수를 체크 하지만 Proc는 갯수 체크를 하지 않고 해당 인자가 들어오지 않을경우 nil 로 처리된다. lambda 를 사용할때 인자의 갯수가 틀리면 ArgumentError가 발생한다.
{% highlight ruby linenos %}
def proc_return
  Proc.new { return "Proc.new"}.call
  return "proc_return method finished"
end

def lambda_return
  lambda { return "lambda" }.call
  return "lambda_return method finished"
end

puts proc_return
puts lambda_return
# => Proc.new
# => lambda_return method finished
{% endhighlight %}
>> Proc 는 return 된후 해당 로직을 종료한다. lambda 는 return 후에도 로직이 계속 진행된다.
위와 같이 Proc와 lambda 의 차이는 둘의 컨셉의 차이라고 할 수 있다.
Proc 은 단지 `Drop In Code snippets` 이지만 lambda 는 method처럼 행동한다. lambda 의 모든 특징은 method의 특징과 매우 닮아 있다.
즉 lambda 의 사용은 method를 사용하는 또하나의 방법이라고 생각할 수 있다.


람다는 파라메터 갯수를 체크한다.
람다는 리턴되어도 계속 진행 되지만 Proc는 진행후 return후 함수가 종료된다.

## Method Object
Ruby 에서는 모든것이 객체일까? Ruby 에는 객체가 아닌것도 존재한다. 위에 설명했던 block도 객체가 아니다. & 와 to_proc 의 과정을 거친후에
Proc 이라는 객체가 된것이다. 또하나 Ruby 에서 객체가 아닌것은 Method 다. Method 그 자체는 객체가 아니다.
Method 를 객체화 시킨것이 Method Object 이다. (Ruby에서 객체가 아닌 또 하나는 Keyword이다.)
{% highlight ruby linenos %}
def function(code)
  puts "function called"
  code.call("You are Proc")
  # puts code.class # Method
end

def testfunc(param)
  puts "lambda called #{param}"
end
function(method(:testfunc))
{% endhighlight %}
method 라는 함수를 사용해 testfunc를 Method Object 로 전환 시킨후에 function 을 실행 시켰다.
code.class 는 Method 라고 나오는 것을 확인 할 수 있다.
Method Object 는 lambda 처럼 행동 하지만 이름없는 method 인 lambda와 다르게 이름이 있는 method라고 할 수 있다.

## TroubleSome
{% highlight ruby linenos %}
def generic_return(code)
  code.call
  return "generic_return method finished"
end

puts generic_return(Proc.new { return "Proc.new" })
puts generic_return(lambda { return "lambda" })
# => *.rb:6: unexpected return (LocalJumpError)
# => generic_return method finished
{% endhighlight %}
Proc 을 사용할 경우에 call 후에 return 이 놓이게 되면 interpreter 가 에러를 발생 시킨다.
Proc 을 사용할 경우 return 사용에 주의 해야 한다.
{% highlight ruby linenos %}
def blockFunc(*args,&block)
  puts block.class
  yield
end
blockFunc "hello" "hi" do
  puts "blockfunc"
end
{% endhighlight %}
block을 사용할때는 block 인자는 맨 마지막 인자로 사용해야 한다.

## Conclusion
Ruby에 closure 타입에는 block Procs lambdas method 가 있다. block 과 Procs 는 drop-in code snippet 으로 사용되고
lambdas 와 methods 는 함수 처럼 작동 한다.
위와 같은 Ruby에 Closure 를 잘 사용하면 루비를 좀더 강력하고 효과적으로 유연하게 사용 가능하다!

## 추가(2014.2.13) Proc 간단한 선언법
Ruby 2.0 이후 Proc을 선언할때 proc=Proc.new 를 사용하기 보다는 proc= ->(input){} 처럼 좀더 간단히 사용한다.!
{% highlight ruby linenos %}
Blank = ->(input) { input.strip == '' } # 공백문자들로 구성되어 있을경우
Uppercase = ->(input) { (input =~ /[A-Z]/) # 대문자로만 구성되어있는지
FILTER = {
  Blank => "반킨이야",
  Uppercase => "대문자로만 구성되어 있어"
  ...
}
FILTER.detect { |proc,| proc[input]}.last # 필터에 걸린 마지막 찾음!
{% endhighlight %}
## Refreence
[Robeert Sosinski](http://www.robertsosinski.com/2009/01/11/the-difference-between-ruby-symbols-and-strings/)
[Closures In Ruby](http://innig.net/software/ruby/closures-in-ruby)

