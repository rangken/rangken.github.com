---
layout: post
title:  "HTTP 1.1 Headers"
date:   2015-01-22 12:30:40 +0900
categories: HTTP
tags: HTTP Web
description: HTTP Header들 정리
---

## HTTP HEADER

### 일반 헤더 (General Headers)
- 클라이언트와 서버 양쪽 모두 사용하는 헤더

___

| 헤더 | 설명 | ETC |
|:--:|:----|:----:|
|Connection| 클라이언트와 서버가 요청/응답 연결에 대한 옵션을 정할수 있게해준다.| Connection: close; Connection: Keep-Alive;|
|Date| 메세지가 언제 만들어졌는지에 대한 날짜와 시간을 제공한다.| Date: Thu, 22 Jan 2015 04:20:18 GMT, 가능한 Date 포맷이 정해져있다|
|MIME-Version| 발송자가 사용한 MIME 버젼 ||
|Transfer-Encoding| 수신자에게 안전한 전송을 위해 메세지에 어떤 인코딩이 적용되었는지||
|Via| 이메시지가 어떤 중개자(프락시, 게이트웨이) 를 거쳐왔는지 보여준다 | |

#### 일반 캐쉬 헤더

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Cache-Control| 메세지와 함께 캐시 지시자를 전달하기 위해 사용한다.|no-cache, no-store,max-age=100 등등 |
|Pragma| 메시지와 함께 지시자를 전달하는 또 다른 방법| HTTP 1.1 에서는 안쓴다|



<br>
<br>

### 요청 헤더 (Request Headers)
- 요청 메시지에서만 의미를 갖는 헤더.

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Client-IP| 클라이언트가 실행된 컴퓨터의 IP| RFC2616 에 정의 되어있지는 않지만 여러 HTTP Client가 보내는값|
|From| 클라이언트 사용자의 메일 주소||
|Host| 요청의 대상이 되는 서버의 호스트명과 포트||
|Referer| 현재의요청 URI 가 들어있었던 문서의 URL 을 제공한다| 뒤로가기를 누르면 보통 해당 URL로 이동됨|
|UA-*| 클라이언트 정보를 보냄| UA-COLOR, UA-CPU, UA-OS, UA-Pixels 등등|
|User-Agent| 요청을 보낸 어플리케이션 이름을 서버에게 전달|User-Agent: curl/7.3.1, 
Mozilla/4.0 MSIE 8.0 Trident/4.0, Mozilla/5.0|

<br>

#### Accept 관련 헤더

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Accept| 서버에게 서버가 보내도 되는 미디어 종류를 말해줌|Accept: text/*, text/html;level=1 |
|Accept-Charset| 보내도 되는 문자집합||
|Accept-Encoding| 보내도 되는 인코딩||
|Accept-Language| 보내도 되는 언어|

<br>

#### 조건부 요청 헤더

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Except| 클라이언트가 요청에 필요한 서버의 행동을 열거할수 있게 해준다.| |
|If-Match| 문서의 엔터티 태그가 주어진 엔터티 태그와 일치하는 경우에만 문서 가져옴| |
|If-Modified-Since| 주어진 날짜 이후에 리소스가 변경되지 않았으면 요청을 제한함| |
|If-None-Macth| 문서의 엔터티 태그가 주어진 앤터티 태그와 일치하지 않는 경우에만 문서 가져옴| |
|If-Range| 문서의 특정범위에 대한 요청을 할수 있게 해준다.| |
|If-Unmodified-Since| 주어진 날짜 이후에 리소그가 변경되었다면 요청을 제한한다.| |
|Range| 서버가 범위 요청을 지원한다면 리소스에 대한 특정 범위를 요청한다.| Chunked 로 문서를 나눠서 가져올때 Range를 통해 byte 범위를 지정해줄수 있다. | 

<br>

#### 요청 보안 헤더 : 보안을 위한 헤더

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Authorization| 클라이언트가 서버에게 제공하는 인증 그자체에 대한 정보||
|Cookie| 클라이언트가 서버에게 토큰을 전달할때 사용된다.| |
|Cookie2| 요청자가 지원하는 쿠키의 버젼을 알려줄때 | |

<br>

#### 프락시 요청 헤더 : 인터넷에서 이상한 프락시들이 생기면서 프락시들의 기능을 돕기 위해 만들어진 헤더 

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Max-Forwards| 요청이 원서버로 향하는 과정에서 다른 프락시나 게이트웨이로 전달될수 있는 최대 횟수| |
|Proxy-Authorization| 프락시에서의 인증||
|Proxy-Connection | Proxy 가 Connection 을 인식하지 못하기 때문에 그에대한 보완으로 | | 

<br>
<br>

### 응답 헤더 (Response Heders)

____

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Age| 응답이 얼마나 오래 되었는지| 응답이 중개자(프락시 캐시)를 통해서 왔을때|
|Public| 서버가 특정 리소스에 대해 지원하는 요청 메서드 목록| |
|Retry-After| 현재 리소스가 사용 불가능한 상태일때 언제 가능해지는지 | |
|Server| 서버 어플리케이션의 이름과 버젼 | |
|Title| HTML 문서에서 주어진 것과 같은 제목 | |
|Warning| 경고 메세지 | | 

<br>

#### 협상 헤더

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Accept-Range| 서버가 자원에 대해 받아들일 수 있는 범위의 형태| |
|Vary| 응답에 영향을 줄수 있는 헤더들의 목록을 클라이언트에게 알려줌|  Vary: Accept-Encoding,User-Agent|

<br>

#### 응답 보안 헤더

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Proxy-Authenticate| 프락시에서 클라이언트로 보낸 인증 요구의 목록| |
|Set-Cookie| 서버가 클라이언트를 인증할수 있도록 클라이언트 측에 토큰을 설정하기 위해 사용한다| | 
|Set-Cookie2| | |
|WWW-Authenticate| 서버에서 클라이언트로 보낸 인증 요구의 목록| |

<br>

### 엔터티 헤더 (Entity Headers)
- 엔터티, 본문에 대한 헤더

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Allow| 이엔터티에 대해 수행될수 있는 요청 메서드들을 나열한다| |
|Locatin | 클라이언트에게 엔터티가 실제로 어디에 위치하고 있는지 말해준다. 수신자에게 리소스에 대한 위치를 알려줄때 사용| |

<br>

#### 콘텐츠 헤더

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|Content-Base| 본문에서 사용된 상대 URL 을 계한하기 위한 Base URL| |
|Content-Encoding| 본문에 적용된 어떤 인코딩 | |
|Content-Language| 본문을 이해하는데 가장 적절한 언어| |
|Content-Length| 길이나 크기 | | 
|Content-Location| 리소스가 실제로 어디 위치하는지| |
|Content-MD5| 본문의 MD5 | |
|Content-Range| 전체 리소스에서 이엔터티가 해당하는 범위를 바이트 단위로 표현 | |
|Content-Type| 이본문이 어던 종류의 객체인지 | |

<br>

#### 엔터티 캐싱 헤더

___

|헤더 | 설명 | ETC|
|:--:|:----|:----:|
|ETag| 이 엔터티에 대한 엔터티 태그 | | 
|Expires| 이 엔터티가 더이상 유효하지 않아 원본을 다시 받아와야 하는 일시| |
|Last-Modified| 가장 최근에 이 엔터티가 변경된 일시 | | 



#### Reference
- [HTTP 완벽가이드](http://book.naver.com/bookdb/book_detail.nhn?bid=8509980)
- [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616.txt)



