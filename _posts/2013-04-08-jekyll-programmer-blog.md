---
layout: post
title: "Jekyll-Programmer Blog"
tagline: "Static blog engine Jekyll"
category: Programming
tags: [Ruby, Blog]
---

## Programmer Blog Jekyll
프로 그래머를 위한 블로그 [Jekyll(지킬)](https://github.com/mojombo/jekyll)에 대한 포스팅이다.

{% githubrepo jekyll/jekyll %}

많은 사람들이 블로그로 wordpress 를 사용하지만 jekyll 는 wordpress 에 비해서 좀더 개발자
스러운 블로그 engine 이라고 할수 있겠다. 뭐 engine이라고 할만큼 대단하지 않고 단순한 parser라고 표현하는게 좋겠다.
wordpress 처럼 특정 Database가 필요하지 않고 웹서버가 필요하지않다.
단순한 static 한 html 파일만 가지고 블로그를 만들수 있다.
Database 사용없이도 tag와 category 기능을 단순 markup으로 구현되어져 있다.
단순 몇개 마크업만 배우면 손쉽게 posting을 할수 있다!
간단하지만 개발자 스럽다!<br>

### Jekyll 장점
- Database가 필요없다.
- 서버를 변경하기 쉽다. 그냥 jekyll 폴더만 옴기면 된다.
- 따로 웹서버가 필요없다. Github에서 무료 hosting 을 운영하며 단순 commit 만으로 블로그 운영 가능하고 히스토리도 변경 가능하다.우와!

Jekyll은 Github에서 근무중인 [Tom Preston-Werner(mojombo)](https://github.com/mojombo) 라는 사람이 개발했다.
Github에서는 User와 Organization을 위한 페이지를 제공해주고 있다.<br>

간단히 설명하면<br>
*User Page를 만들기 위해서는 `user/user.github.com `으로 repository 를 만들어야한다.
*Organization Page를 만들기 위해서는 `gh-pages` branch 를 생성해주면 된다.
*자세한 설명은 [Github Help](https://help.github.com/articles/user-organization-and-sproject-pages) 참고

Github에서는 이런 User/Organization Page 서비스를 위해서 기본으로 Jekyll 을 사용하고 있다.
그래서 현재 많은 개발자들이 Github Jekyll 을 통해 개인 블로그를 운영중이다.
Jekyll 은 Ruby로 되어있다.(현재 Github에서 3번째로 유명한 Ruby 프로젝트)
Ruby가 설치되어있다면 Gem을 통해 간단하게 설치 할 수 있다. <br>

## Install Jekyll비

###루비설치
Jekyll 을 설치하기 위해서는 Ruby가 설치되어 있어야 한다.
맥의 경우 기본적으로 루비가 설치 되어있지만 모르시는 분들은
루비 버젼 관리툴인 rbenv rvm 을 이용하시길 추천합니다.
- [rbenv](https://github.com/sstephenson/rbenv)
- [rvm](https://github.com/wayneeseguin/rvm)

ruby -v 을 통해 루비 버젼이 1.9.1 이상인지 확인한다.<br>

## Jekyll 설치
ruby package 관리툴인 gem 을 이용해 설치한다.
{% highlight ruby linenos %}
gem install jekyll
# markdown RDiscount
gem install rdiscount
{% endhighlight %}
<br>
## Dependencis & Pygments 설치
Jekyll 에 종속적인 패키지가 몇개 있다.gem을 통해 설치하는게 좋다!<br>
[Classifier](https://github.com/cardmagic/classifier)
[Directory Watcher](https://github.com/TwP/directory_watcher.git)
[Kramdown](https://github.com/gettalong/kramdown)
[Liquid](https://github.com/Shopify/liquid)
[Maruku](https://github.com/bhollis/maruku)
<br>
Kramdown Maraku RDiscount 중에 한개만 설치하면 되고
Liquid - template 엔진
Directory Watcher - Posting 한 글을 수정하면서 계속 확인 할 수 있게 해줌
은 꼭 철치하는게 좋겠다.

[pygments](http://pygments.org) 는 <code>{% raw %}{% highlight %}{% endraw %}</code> 을 이용해
code에 맞는 syntax highlingt 기능을 사용하기 위한 python 라이브러리이다.
python 패키지 관리툴은 pip ,easy_install 을 이용해 설치
- pip install pygments
- easy_install Pygments
pygments를 설치할경우

{% highlight ruby linenos %}
{% raw %}
{% highlight ruby linenos %}
def foo
  puts 'foo'
  # comment
end
{% endhighlight %}
{% endraw %}
->
def foo
  puts 'foo'
  # comment
end
{% endhighlight %}
처럼 syntax highlighting 을 사용 할 수 있다.
## Usage
{% highlight ruby linenos %}
.
|-- _config.yml
|-- _includes
|-- _layouts
|   |-- default.html
|   |-- post.html
|-- _posts
|   |-- 2007-10-29-why-every-programmer-should-play-nethack.textile
|   |-- 2009-04-26-barcamp-boston-4-roundup.textile
|-- _site
|-- index.html
{% endhighlight %}
Jekyll 에 기본 파일 구조는 위처럼 되어져 있다.
config.yml은 기본적인 설정 내용을 담고 있다. <br>
`markdown: [engine]` 을 이용해 Maraku에 사용할 엔진을 지정해 줄 수 있다(rdiscount,redcarpet)<br>
`Pygments: [boolean]` 을 이용해 pygment을 사용할지 말지 결정 할 수 있다.<br>
`permalink: /:categories/:year/:month/:day/:title` Post된 글의 URL이 어떤 모습이 될지 정할 수 있다.<br>
그이외의 속성은 [Github Wiki](https://github.com/mojombo/jekyll/wiki/Configuration) 에서 확인 할 수 있다.<br>

jekyll 에서는 posts 폴더에 html파일을 생성하고 jekyll 서버를 작동 시키면 <br>
jekyll parsing 을 통해 site에 실제 파일을 생성한다. <br>
site에 있는 파일은 서버를 run 시킬때 마다 자동으로 생성되는 파일이므로 수정하지 않아도 된다.<br>

## Posting
{% highlight ruby linenos %}
---
layout: post
title: "Jekyll Programmer Blog"
tagline: "Static blog engine Jekyll"
category: Programming
tags: [Ruby Blog]
---
{% endhighlight %}
위 내용은 본 포스팅에 대한 내용을 담고 있다. 해당 post 파일 맨 위에 위치하게 된다.
layout 은 layout 폴더에 기본 바탕이 되는 html 파일을 설정해 준다.
layout에 content tag안에 해당 post 가 들어가게 된다.
title tagline 은 타이틀 섭타이틀 정도로 생각하면 되겠다.
category 는 해당 글에 category 다. 여기에 입력해줄 경우 자동으로 카테고리를 생성해준다 우와!
tag는 [] 를 통해 array형태로 여러개 설정해 줄수 있다.

## Markdown
Jekyll 에 가장큰 장점은 Markdown 으로 쉽게 글을 쓸수 있다는것이다!!
Markdown 은 .md파일로 간단한 태그를 이용해 html 코드를 생성할수 있다.
{% highlight ruby linenos %}
# h1
## h2
### h3
> blockquotes
>> netsted blockquotes
*
* List
header
=======
header
-------
{% endhighlight %}
위 내용이외에 많은 Markdown 이 있다!.
[Wikipedia](http://en.wikipedia.org/wiki/Markdown)에 잘 정리되어 있다.(1분이면 모든걸 익힐수 있을만큼 쉽다.)

## Jekyll Bootstrap
Jekyll 가 아무리 간단하고 쉬운 blog 엔진이라고 하더라도 실제로 다 만드는건 영 귀찮은 일이다.
[Jekyll Bootstrap](http://jekyllbootstrap.com) 을 통하면 몇개의 theme 도 제공하고 기본적인 jekyll 로 블로그를 운영하기 위한 틀을 제공한다!
Rakefile 로 포스팅할 글이나 페이지를 만들수 있는 기능을 제공한다.<br>
>rake post title="hello world"<br>
>rake page title="about.md"<br>

## Hosting
	jekyll --server # jekyll 서버를 실행

Jekyll 에 가장큰 장점은 웹서버 없이 단순히 파일만 올리면 서버로써 작동할 수 있다는거다!
더큰 장점은 Github을 통해 호스팅 한다는! <br>
curl -I rangken.github.io 를 통해 Http header를 보면 <br>
Server : github.com 이라고 나오는 것을 확인 할 수 있다! <br>

자신의 github계정 아이디.github.com 으로 repository 를 만든후에
해당 Jekyll 프로젝트를 master 브랜치에 업로드 할 경우
http://아이디.github.io 로 들어가면 확인 할 수 있다. ( 약 10분에 딜레이가 있다!)
자신의 도메인으로 변경도 가능하다!
## TroubleShooting
포스팅을 수정하면서 계속 브라우져를 통해 확인할수 있도록 하는 package인 Directory Watcher 가 작동하지 않을때가 있다.
>sudo gem uninstall directory_watcher && sudo gem install directory_watcher -v 1.4.1
해당 커맨드로 다시 설치해주면 정상 작동한다!<br>
특정 식별자를 그대로 노출하고 싶다면 \\ 를 사용한다.
>\__ 와 같은 식별자도 \\__ 이런식으로 쓸경우 그대로 표현이 가능하다!


## Conclusion
Jekyll 는 현재도 계속 개발중인 프로젝트로 앞으로 많은 개발자들이 Jekyll 를 통해 개발자 블로그를
운영할 것이다!
관심있는 개발자라면 Jekyll 를 통해 블로그를 운영 할 것을 추천함~


