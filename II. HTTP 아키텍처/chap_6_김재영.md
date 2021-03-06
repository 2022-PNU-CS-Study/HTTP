# chap 6 (프락시)

> **프락시는 클라이언트와 서버 사이에 위치하여 그들 사이의 HTTP 메시지를 정리하는 중개인이다.**

## 웹 중개자

- 웹 프락시 서버는 클라이언트의 입장에서 트랜잭션을 수행하는 중개인이다.
- 웹 프락시가 없다면 클라이언트는 서버와 직접 대화한다.
- 웹 프락시는 서버처럼 메시지를 받거나 클라이언트처럼 메시지를 요청할 수 있다.

### 프락시 vs 게이트웨이

- 프락시: 같은 프로토콜을 사용하는 둘 이상의 애플리케이션을 연결
- 게이트웨이: 다른 프로토콜을 사용하는 둘 이상의 애플리케이션을 연결

하지만 실질적으로 프락시와 게이트웨이의 차이점은 모호하다. 프락시 또한 때때로 약간의 프로토콜 변환을 하기도 한다.

## 왜 프락시를 사용하는가?

프락시 서버는 클라리언트와 실제 서버 사이에서 모든 HTTP 트래픽을 들여다보고 수정할 수 있다. 그래서 프락시를 잘 이용하면 보안을 개선하고, 성능을 높여주며, 비용을 절약할 수 있다.

*어린이 필터*

- 초등학교의 필터 프락시는 어린이들에게 교육 콘텐츠는 제공하면서 성인 콘텐츠는 차단할 수 있다.

*보안 방화벽*

- 보안을 강화하기 위해 프락시 서버를 사용한다.

*웹 캐시*

- 인기 있는 문서의 로컬 사본을 관리하고 해당 문서에 대한 요청이 오면 빠르게 제공한다.

*대리 프락시*

- 웹 서버인 것처럼 위장한 대리 프락시는 진짜 웹 서버 요청을 받지만 웹 서버와는 달리 요청 받은 콘텐츠의 위치를 찾아내기 위해 다른 서버와 커뮤니케이션을 한다.

*콘텐츠 라우터*

- 인터넷 트래픽 조건과 콘텐츠의 종류에 따라 요청을 특정 웹 서버로 유도하는 콘텐츠 라우터로 동작할 수 있다.

*트랜스코더*

- 콘텐츠를 클라이언트에게 전달하기 전에 본문 포맷을 수정할 수 있다.
- 크기를 줄이기 위해 자신을 거쳐가는 GIF 이미지를 JPG 이미지로 변환할 수 있다.
- 이미지의 색 강도를 줄이거나 텍스트 파일을 압축하고, 문서를 외국어로 변환할 수 있다.

*익명화 프락시*

- HTTP 메시지에서 신원을 확인할 수 있는 헤더들(IP, FROM 헤더, REFERER 헤더, 쿠키, URI 세션)을 제거함으로써 익명성을 보장한다.

## 프락시 서버의 위치

> 프락시는 어떻게 사용하느냐에 따라 어디에든 배치할 수 있다.

![Image](./asset/chap_6_%EA%B9%80%EC%9E%AC%EC%98%81_asset_1.png)

*출구*(Egress) *프락시*

- 로컬 네트워크와 더 큰 인터넷 사이를 오가는 트래픽을 제어하기 위해 로컬 네트워크의 출구에 프락시를 위치시킨다.

*접근*(입구) *프락시*

- 고객으로부터의 모든 요청을 종합적으로 처리하기 위해 프락시는 ISP 접근 지점에 위치하기도 한다.
- 사용자들의 다운로드 속도 개선과 인터넷 대역폭 비용을 줄이기 위해 캐시 프락시를 사용

대리 프락시(리버스 프락시)

- 웹 서버들의 바로 앞에 위치하여 모든 요청을 처리하고 필요할 때만 웹 서버에게 자원을 요청한다.
- 웹 서버의 이름과 IP 주소로 스스로를 가장하기 때문에, 모든 요청은 서버가 아닌 프락시로 간다.

### 프락시의 연쇄

![Image](./asset/chap_6_%EA%B9%80%EC%9E%AC%EC%98%81_asset_2.png)

메시지는 원 서버에 도착할 때 까지 여러 프락시를 거쳐 이동한다. 하지만 어떤 프락시들을 통과할지나 그 순서가 항상 정적인 것은 아니다. 그때 그때 판단 근거에 따라 얼마든지 달라질 수 있다.

### 프락시가 프락시를 찾아가는 방법

어떻게 원서버로 요청한 메시지가 프락시한테 갈까?

**클라이언트를 수정한다**

- 의도적으로 원 서버가 아닌 프락시 서버로 요청을 보낸다.

**네트워크를 수정한다.**

- 네트워크 인프라를 가로채서 웹 트래픽을 프락시로 가도록 조정
- HTTP 트래픽을 지켜보고 가로채어 클라이언트 모르게 트래픽을 프락시로 보내는 **스위칭 장치 / 라우팅 장치**가 필요 → 이를 인터셉트 프락시라 부른다.

**DNS 이름공간을 수정한다.**

- DNS 이름 테이블을 수동으로 편집하거나, 특별한 동적 DNS 서버를 이용해서 조정

**웹 서버를 수정한다.**

- 웹 서버는 HTTP 리다이렉션 명령(305)을 클라이언트에게 돌려줌으로써 클라이언트의 요청을 프락시로 리다이렉트 한다

## 프락시 요청의 특징들

### 프락시 URI는 서버 URI와 다르다

클라이언트가 웹 서버로 요청을 보낼 때,

> GET /index.html HTTP/1.0
User-Agent: SuperBrower v1.3

클라이언트가 프락시로 요청을 보낼 때,

> GET [http://www.google.com/index.html](http://www.google.com/index.html) HTTP/1.0
User-Agent: SuperBrowser v1.3

프락시로 요청을 보낼 경우, 완전한 URI 형태를 갖는다.

- 클라이언트가 프락시를 사용하지 않도록 설정되어 있다면, 부분 URI를 보낸다
- 클라이언트가 프락시를 사용하도록 설정되어 있다면, 완전한 URI를 보낸다

### 프락시는 프락시 요청과 서버 요청을 모두 다룰 수 있다

프락시는 명시적인 프락시 요청에 대해서는 **완전한 URI**를 사용하고 아니면 **부분 URI**를 사용해야한다. 웹 서버 요청의 경우에는 가상 Host 헤더를 사용해야 한다.

규칙은 다음과 같다.

- 완전한 URI가 주어졌다면, 그것을 사용
- 부분 URI가 주어졌고 Host 헤더가 있다면, Host 헤더를 이용
- 부분 URI가 주어졌으나 Host 헤더가 없다면,
   - 대리 프락시라면, 프락시에 실제 서버의 주소와 포트번호가 설정되어있다
   - 이전에 인터셉트 프락시가 가로챈 트래픽에서 사용한 IP 주소와 포트번호를 사용
   - 모두 실패라면, 충분한 정보가 없는것이므로 반드시 에러 메시지를 반환해야한다

### 전송 중 URI 변경

기본적으로 프락시 서버는 URI 변경을 하지 않는 것이 원칙이다. HTTP 포트를 명시적으로 :80으로 한다던가 요청을 이스케이프 하는 것은 무해한 일이라 생각하겠지만, 어떤 부작용을 만들어낼지는 아무도 모른다. HTTP 스펙에도 명시되어 있지만 URL을 전달할 때 절대 경로 고치는 것을 금한다. 예외로 빈 경로를 '/'로 교체하는 것은 허용한다.

### URI 클라이언트 자동확장과 호스트 명 분석

브라우저에 naver 만 입력해도 www.naver.com 으로 이동할 수 있는 이유는 프락시 덕분이다.

## 메시지 추적

프락시가 점점 더 흔해지면서, 서로 다른 스위치와 라우터를 넘나드는 IP 패킷의 흐름을 추적하는 것 못지않게 프락시를 넘나드는 메시지의 흐름을 추적하고 문제점을 찾아내는 것이 필요해졌다.

### Via 헤더

Via 헤더 필드는 메시지가 지나는 각 중간 노드의 정보를 나열한다. 이를 위해 프락시 서버는 자신을 통하는 메시지에 Via 헤더값에 자신을 추가해야한다.

> Via: 1.1 [proxy-62.irenes-isp.net](http://proxy-62.irenes-isp.net), 1.0 [cache.joes-hardware.com](http://cache.joes-hardware.com)

### Server 헤더

Server 응답 헤더 필드는 원 서버에 의해 사용되는 소프트웨어를 알려준다.

> Server: Apache/1.3.14 (Unix) PHP/4.0.4

### TRACE 메소드

HTTP/1.1의 TRACE 메서드는 요청 메시지를 프락시의 연쇄를 따라가면서 어떤 프락시를 지나가고 어떻게 각 프락시가 요청 메시지를 수정하는지 관찰/추적할 수 있도록 해준다.

TRACE 요청이 목적지 서버에 도착했을 때, 서버는 전체 요청 메시지를 HTTP 응답 메시지의 본문에 포함시켜 송신자에게 그대로 돌려보낸다. TRACE 응답이 도착했을 때, 클라이언트는 서버가 받은 메시지와 그 메시지가 지나간 프락시들의 목록(Via 헤더 안)을 검사할 수 있다.

TRACE 응답의 Content-Type은 message/http이며 상태는 200 OK 이다

### 프락시 상호 운용성

클라이언트, 서버, 프락시는 각기 다른 버전의 HTTP 명세를 따를 수 있다. 다른 프로토콜을 따르고 있을 수도 있다. 그럼에도 프락시는 이들이 올바르게 통신할 수 있도록 중개 역할을 해내야한다.

### 지원하지 않는 헤더와 메서드 다루기

프락시는 이해할 수 없는 헤더는 반드시 그대로 전달해야 하며, 같은 이름의 헤더 필드가 여러 개 있는 경우, 그들의 상대적인 순서도 반드시 유지해야 한다.

### OPTIONS 메소드

HTTP OPTIONS 메서드는 서버나 웹 서버의 특정 리소스가 어떤 기능을 지원하는지 클라이언트(혹은 프락시)가 알아볼 수 있게 해준다.

![Image](./asset/chap_6_%EA%B9%80%EC%9E%AC%EC%98%81_asset_3.png)

### Allow 헤더

서버는 자신이 실제로 지원하는 메서드를 Allow 엔티티 헤더에 넣어서 응답한다.

