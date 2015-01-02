---
layout: post
title: "NodeJS Introduce"
description: ""
category: Backend
tags: [NodeJS, Web]
---

![NodeJS Logo](/assets/images/nodejs.png)

## NodeJS ?

NodeJS 란 서버 사이드 자바스크립트 기술로 구글에서 구글 크롬에서 사용한  javascript 엔진인 v8 을 이용해서 이벤트 기반의 서버 프로그래밍이다.
`구글에서 개발한 만큼 차세대 웹 플렛폼`으로 주목 받고 있다. 메모리 소비량이 적고 많은 서비스 요청 처리에도 뛰어난 성능을 보여주고 있다. 요즘 페이스북 월마트 링크드인 같은 대형 싸이트에서도 사용하고 있다!


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
![Rails Logo](/assets/images/rails.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
![Ruby Logo](/assets/images/ruby.png)

## Download & Install
NodeJS 는 구글이 만든 만큼 사용하기 편한 여러 환경이 갖추어져 있다. 기본적인 NodeJS 홈페이지 에 들어가면 각 플랫폼 에서 다운로드 뿐만 아니라 여러 문서들도 볼수 있다.
Mac 과 window 에서는 간단한 설치 프로그램으로 설치가 가능하다.<br>
git 을 사용하고 있다면 <br>
`git clone git://github.com/joyent/node.git` 을 이용해 git 프로젝트를 다운받고
`cd node , ./configure , make , make install` 실행해 준다!
설치가 완료 되었다면 Command Line 에 `node --version` 을 입력해준다. 해당 버젼명이 출력된다면 재대로 설치된것!!
Nodejs 는 패키지 관리 툴로 RubyOnRails 의 gem  처럼 npm 을 사용하는데 기본적으로 같이 설치된다. `npm -v` 를 이용해 npm 도 재대로 설치 되었는지 확인한다.
저처럼 맥을 사용하고 계신 분들이라면 설치경로는
usr/local/bin 에 있다.
usr/local/lib/node_modules 에 들어가면 기본적으로 global 로 설치된 Nodejs 라이브러리 들을 볼 수 있다.
Nodejs 는 기본적으로 해당 프로젝트에 종속적인 모듈과 global 모듈로 모두 설치가 가능하다.

##기본 예제
Nodejs 홈페이지에 가면 기본 예제를 볼 수 있다.
먼저 프로젝트를 만들 폴더로 이동한후
app.js 파일을 만들어준다.
{% highlight bash  linenos %}
var http = require('http');
	http.createServer(function (req, res) {
	res.writeHead(200, {'Content-Type': 'text/plain'`});
	res.end('Hello World\n');
}).listen(1337, '127.0.0.1');
console.log('Server running at http://127.0.0.1:1337/');
{% endhighlight %}
>1 : require('http')  는 nodes 기본 모듈인 http 을 가져오는   메소드 입니다.

>2: createServer 는 http 모듈을 이용해 httpServer 를 생성합니다.  createServer 의 매개변수로 function을 지정할 수 있습니다.

>3 , 4 : 200 번  성공적인 response 로 Hello World 란 출력을 보낸다. text/plain 은 기본적인 텍스트 문서를 출력하기 위함이다. 그이외의 text/html (HTML 문서) , text /css (CSS 문서) , image/png ( png이미지 파일)  , audio/mp3 (MP3 음악 파일) 등을 지정 할 수 있다.

>5: listen( 1337, '127.0.0.1');  localhost:1337 localhost 의 1337 번 포트로 해당 서버를 listen 한다. 해당 예제를 생성후에 Command Line에 node app 혹은 node app.js 를 입력해주면 서버를 실행 시킬 수 있다.

>6 : NodeJS 는 javascript 엔진인 만큼 자바에서 출력함수인 console.log를 사용 할 수 있다. 물론 setInterval 같은 함수들도 사용가능하다. 브라우져를 통해 http://localhost:1337 을 입력하거나 curl http://127.0.0.1:1337 을 이용하면 hello world 를 볼수 있을 것이다.

## HTTP
NodeJS 의 Http 는 아주 기본적인 모듈이다.
기본적으로 HTTP 객체에서는 여러 이벤트 메소드들을 가지고 있다.  javaSciprt 언어의 특징인 이벤트를 사용해서 쉽게 각 상황을 캐치 할 수 있다.

>request - 클라이언트가 요청 할 때 발생하는 이벤트
>connection - 클라이언트가 접속할 때 발생하는 이벤트
>close - 서버 종료될때
>checkContinue - 클라이언트가 지속적인 연결을 하고 있을때
>clientError - 클라이언트에서 오류가 발생할 때 발생 하는 이벤트
{% highlight bash linenos %}
var http = require('http');

var server = http.createServer();
server.on('request',function(){
	console.log('Requst On');
});
server.on('connection',function(){
	console.log('Connection On');
});
{% endhighlight %}

## Request , Response
NodeJS  의 Http 모듈은 기본적으로 req 와 res 두개의 매개변수를 가지고 있다.
**response**
>writeHead(statusCode,object) - 응답 헤더를 작성 <br>
>end([data],[encoding]) - 응답 본문을 작성 <br>

**request**
>method - 클라이언트에서 요청한 방식 ( GET ,POST) <br>
>url - 클라이언트가 요청한 url <br>
>headers - 요청 메세지 해더 <br>
>trailers - 요청 메세지 트레일러  <br>
>httpVersion - HTTP 프로토콜 버젼  <br>
{% highlight bash linenos %}
var http = require('http');
var url = require('url');

http.createServer(function(req,res) {
	var query = url.parse(req.url,true).query
	console.log(query);
	res.writeHead(200,{'Content-Type':'text/html'});
	res.end('<h1>' + JSON.stringify(query) + '</h1>');
}).listen(8001,function(){
	console.log('Server Running at http://127.0.0.1:8001');
});
{% endhighlight %}
url 모듈을 사용해서 url 을 parsing 할 수 있다.
curl 을 이용해 curl http://127.0.0.1:8001?name=rangken&message=helloworld
를 입력해보면 각 param 들이 parsing 되는 것을 확인 할 수 있다.
JSON.stringify(string) 메소드는 해당 json 을 string 으로 리턴해주는 함수이다.

## File System ( fs )
NodeJS  에서 파일을 다룰 때 쓰는 module 이다.
fs 를 이용해 mp3 파일을 읽어 출력 하는 예제이다.
{% highlight bash linenos %}
var fs = require('fs');
var http = require('http');
http.createServer(function(req, res){
	fs.readfile('music.mp3',function(error, data){
	res.writeHead( 200, {'Content-Type' : 'audio/mp3' });
	res.end(data);
});
}).listen(8001,function(){
	colsole.log('Server running at http://127.0.0.1:8001');
});
{% endhighlight %}
앞 예제와 비슷하고 readfile 을 이용해 mp3 파일을 읽은후 audio/mp3 파일로 출력 한다!
