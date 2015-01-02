---
layout: post
title: "Curl-Command Line Tool"
tagline: "Command Line Tool - curl"
description: ""
category: ETC
tags: [Curl]
---
![Curl Logo](http://curl.haxx.se/pix/curl-refined.jpg)
<br>
[CURL](http://curl.haxx.se) 은 HTTP , HTTPS , FTP ,FTPS,IMAP 등 여러 프로토컬을 지원하는 Command Line Tool 이다.
Command Line Tool 이란 Terminal 을 통해 실행 시킬 수 있는 프로그램이라는 뜻이다. <br>
libCurl 이란 C 기반으로 된 curl 라이브러리를 말한다.<br>
이번 포스팅은 Command Line Tool 로써 CURL 에 대한 내용이다. 가장 많이 쓰는 HTTP 와 FTP 사용 옵션
## HTTP
{% highlight bash linenos %}
curl "http://naver.com" # http://naver.com GET Request
curl -I  # Header만 출력
curl -i # Header 와 Body도 출력
curl -v # -v옵션은 자세한 과정을 출력함
curl -T # 파일 업로드
curl -d "name=rangken" http://naver.com #: naver.com 에 name=rangken 을 POST 로 Request
#(curl --data 와 같다 , curl --data-urlencode url Encoding 한다.)
curl -X (POST/DEL/GET/UPDATE) # 지정된 메소드로 Request
curl -b "name=rangken" http://naver.com # Cookie 설정
{% endhighlight %}
## FTP 다운로드
{% highlight bash linenos %}
curl -v -u 'FTP아이디:FTP비번' # ftp://주소/경로 -o 출력할 파일
ex) curl -v -u 'rangken:pwd' ftp://rangken.com/ftp/download.zip -o Download/download.zip
-v : 자세한 옵션 출력
-u : 인증 아이디를 rangken 으로 비번을 pwd 로 인증함
( ftp://아이디:비번@주소/경로 형식으로 인증도 가능함)
- ftp://rangken.com 이 FTP 주소 /ftp/download.zip 이 경로 이다. ( 여기서 경로는 각계정에 따른 상대 경로이다. root 계정과 개인 계정은 서로 기본 경로가 다르다)
- -o 출력 파일 설정
{% endhighlight %}
## FTP 업로드
{% highlight bash linenos %}
curl -T download.zip ftp://아이디:비번@주소/ftp/ --disable-epsv
-T download.zip : 현재 경로에 download.zip 을 해당 주소에 /ftp/ 폴더에 업로드
- disable-epsv : passive mode 설정
- 여러게 파일 올리려면?
curl -T "{file1,file2,file3}" ftp://아이디:비번 ..
{% endhighlight %}

