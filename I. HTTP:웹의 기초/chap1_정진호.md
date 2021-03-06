# HTTP:웹의 기초
- [HTTP:웹의 기초](#http웹의-기초)
  - [HTTP 개론](#http-개론)
  - [URL와 리소스](#url와-리소스)
    - [URL(Uniform Resource Locator)](#urluniform-resource-locator)
    - [URL 문법](#url-문법)
    - [Scheme 종류](#scheme-종류)
  - [HTTP 메시지](#http-메시지)
    - [요청 메시지 구성요소](#요청-메시지-구성요소)
    - [응답 메시지 구성요소](#응답-메시지-구성요소)
    - [메서드](#메서드)
      - [안전한 메서드(Safe Method)](#안전한-메서드safe-method)
    - [헤더](#헤더)
  - [커넥션 관리](#커넥션-관리)
    - [TCP 커넥션](#tcp-커넥션)
      - [http 요청 시 플로우](#http-요청-시-플로우)
      - [특징](#특징)
      - [TCP 커넥션 유지](#tcp-커넥션-유지)
      - [TCP 소켓 프로그래밍](#tcp-소켓-프로그래밍)
    - [TCP 성능](#tcp-성능)
      - [지연 시간 발생 지점](#지연-시간-발생-지점)
      - [성능 고려 사항](#성능-고려-사항)
      - [TCP 커넥션 핸드쉐이크 지연](#tcp-커넥션-핸드쉐이크-지연)
        - [핸드쉐이크 순서](#핸드쉐이크-순서)
      - [확인 응답 지연](#확인-응답-지연)
      - [TCP 느린 시작(slow start)](#tcp-느린-시작slow-start)
      - [네이글(Nagle) 알고리즘과 TCP_NODELAY](#네이글nagle-알고리즘과-tcp_nodelay)
      - [TIME_WAIT의 누적과 포트 고갈](#time_wait의-누적과-포트-고갈)
    - [HTTP 커넥션 관리](#http-커넥션-관리)
      - [순차적 트랜잭션 처리 지연](#순차적-트랜잭션-처리-지연)
    - [커넥션 끊기](#커넥션-끊기)
      - [우아하게 커넥션 끊기](#우아하게-커넥션-끊기)
## HTTP 개론
- http: 신뢰성있는 전송 프로토콜
- 미디어 타입
  - MIME(Multipupose Internet mail Extensions)
    - 다른 전자 메일 시스템 사이 통신을 지원하기위해 설계
    - 웹 서버가 객체 데이터를 전송하기 위해서 사용됨
- URI(Uniform Resource Identifier)
  - URL(Uniform Resource Locator): 리소스의 구체적 위치
  - URN(Uniform Resource Name): 리소스의 위치와 상관없이 사용되는 고유한 이름
- 트랜잭션
  - (요청 + 응답)으로구성
- TCP/IP
  - HTTP는 신뢰성을 TCP/IP에 의존
  - TCP는 오류없는 전송, 순서, 조각나지않는 데이터 스트림 보장
- 프로토콜 버전
  - HTTP/0.9    : GET 메소드만 지원
  - HTTP/1.0    : 버전 번호, 헤더, 추가 메서드 멀티미디어 객체 추가
  - HTTP/1.0+   : "keep-alive" 커넥션, 가상 호스팅, 프록시 연결
  - HTTP/1.1    : 성능 최적화
  - HTTP/2.0    : 구글의 SPDY 프로토콜을 기반으로 설계
## URL와 리소스
### URL(Uniform Resource Locator)
- 인터넷의 리소스를 가리키는 표준 이름
- URL은 {sheme}://{address}/{resource}의 형태를 가진다.
  - http://www.google.com/index.html 을 요청한다고 가정했을 때
  - http://는 scheme을 의미하며 http 프로토콜을 사용함을 나타낸다.
  - www.google.com은 서버의 위치를 의미한다.
  - /index.html은 요청하고자 하는 리소스의 경로를 의미한다.
### URL 문법
URL을 scheme에 따라서 다른 문법을 가진다. URL 문법을 정규화하면 

> \<scheme\>://\<username\>:\<password\>@\<host\>:\<port\>/\<path\>;\<parameter\>?\<query\>#\<fragment\>

이렇게 나타낼 수 있다.

| 구성요소  | 설명 | 기본값 |
|-------- |-----                              |------|
|scheme   | 리소스에 접급할 때 사용하는 프로토콜을 의미 | 없음 |
|username | 특정 스킴에서 사용하는 사용자 이름 | anonymous|
|password | 사용자의 비밀번호| 이메일 주소|
|host     | 서버의 호스트명 또는 IP 주소 | 없음 |
|port     | 서버가 열어놓은 포트 번호, 스킴에 따라 기본 포트가 다르다 | 스킴에 따름 |
|path     | 서버 내의 리소스의 위치 | 없음|
|parameter| 특정 스킴에서 사용하는 파라미너 | 없음|
|query    | 스킴에서 질의 요소를 작성하는데 쓰임| 없음|
|fragment | 리소스의 조각이나 일부분을 가리킴 | 없음 |

### Scheme 종류
- http: Hypertext Transfer protocol
- https: http 커넥션 양 끝단에서 암호화하기 위한 보안 소켓 계층(Security Sockets Layer, SSL)이 있다.
- mailto: 이메일 전송을 위함 스킴
- ftp: File Transfer Protocol로 파일 요청을 위해서 사용된다. 
- rtsp, rtspu: Real Time Streaming Protocol로 오디오 및 비디오와 같은 미디어 리소스 전달을 위해 사용된다. rtspu는 UDP를 사용함을 나타낸다.
- file: 호스트 기기에 바로 접근할 수 있는 파일을 나타낸다. 
- news: 특정 문서나 뉴스 그룹에 접근하는데 사용된다. 
- telnet: 대화형 서비스에 접근하는데 사용된다. 

## HTTP 메시지
HTTP 메시지 : 앱 사이에 주고받은 데이터 블록
- 인바운드: 요청하는 방향
- 아웃바운드: 응답하는 방향
- 다운스트림: 요청 -> 응답 흐름
- 업스트림: 요청 -> 읍답의 역행
### 요청 메시지 구성요소
시작줄: <메서드> <요청 URL> <버전>

헤더: <헤더>

본문: <엔티티 본문>
### 응답 메시지 구성요소
시작줄: <버전> <상태코드> <상태 메시지>

헤더: <헤더>

본문: <엔티티 본문>
### 메서드
#### 안전한 메서드(Safe Method)
GET, HEAD는 서버의 상태를 바꾸지 않기 때문에 안전한 메서드이다.
### 헤더
헤더와 메서드는 클라이언트가 헤더에 어떤 기능을 요청하는지를 나타내기 위해 사용된다.
- 일반 헤더: 클라이언트/서버 양쪽 모두 사용
- 요청 헤더: 요청 메시지를 위한 헤더
- 응답 헤더: 클라이언트에게 정보를 제공하기 위한 헤더
- 엔티티 헤더: 엔티티 본문에 대한 헤더, Content-Type와 같이 본문이 어떤 타입인지 알려준다.
- 확장 헤더: 개발자들에 의해 생성된 헤더, 특정 기능을 위해 사용된다.
- 일반 캐시 헤더: 클라이언트가 서버로부더 객체를 새로 가져오는 대신 로컬 복사본으로 캐시할 수 있도록 하는 헤더
- [Mozilla HTTP Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)

## 커넥션 관리
### TCP 커넥션
#### http 요청 시 플로우
1. 브라우저가 URL 에서 호스트명 추출
2. 호스트면에 대한 IP 주소를 찾음
3. 브라우저가 포트 번호 획득
4. 브라우저가 IP:포트로 TCP 커넥션 생성
5. 브라우저가 서버로 HTTP 요청
6. 브라우저가 서버로부터 HTTP 응답
7. 브라우저가 커넥션 끊음
#### 특징
- 신뢰할 수 있다.
- HTTPS 사용시에 HTTP 레이어와 TCP 레이어 사이에 SSL(TLS) 계층이 있다.
- TCP는 메시지를 세그먼트 단위로 분리후 IP 패킷으로 전송한다.
#### TCP 커넥션 유지
- 컴퓨터는 여러개의 TCP 커넥션을 유지한다.
- TCP는 포트를 통해서 커넥션을 유지한다.
- 구성요소
  - 발신지 ip 주소
  - 발신지 포트
  - 수신지 ip주소
  - 수신지 포트
#### TCP 소켓 프로그래밍
운영체제는 tcp 커넥션 생성을 위해서 여러 api 를 지원한다. 소켓 api를 통해서 tcp 엔드포인트 데이터 구조를 생성하고 원격 서버의 tcp 엔트포인트와 연결하여 데이터 스트림을 읽고 쓸 수 있다. 
- 소켓 커넥션 연결 과정
  - 서버) 새로운 소켓 생성(socket)
  - 서버) 80포트로 소켓 연결
  - 서버) 소켓 커넥션 허가(listen)
  - 서버) 커넥션을 기다림(accept)
  - 클라이언트) ip주소와 포트를 얻는다.
  - 클라이언트) 새로운 소켓 생성(socket)
  - 클라이언트) 서버의 ip:포트로 연결(connect)
  - 서버) 애플리케이션 커넥션 통지
  - 서버) 요청을 읽는다.(read)
  - 클라이언트) 성공적으로 연결
  - 클라이언트) http 요청 송신(write)
  - 클라이언트) http 응답을 기다림(read)
  - 서버) http 요청 메시지 처리
  - 서버) http 응답 전송
  - 서버) 커넥션 닫기
  - 클라리언트) http 응답 처리
  - 클라이언트) 커넥션 닫기 
### TCP 성능
HTTP 트랜젝션에서 대부분의 지연시간은 연결을 형성하는데에서 발생한다. 그렇기 때문에 HTTP 성능을 높이기 위해서는 TCP 성능을 높여야한다. 
#### 지연 시간 발생 지점
1. URI를 사용하여 연결시 DNS를 통해 ip:포트를 확득하는 시간
2. 클라리언트가 서버로 TCP 커넥션 요청을 응답받는 시간
3. 클라이언트는 HTTP 요청 후 서버에서 처리하는 시간
4. 서버에서 클라이언트로 응답을 보내는 시간
#### 성능 고려 사항
-  TCP 커넥션 핸드쉐이크
-  인터넷 혼잡을 제어하기 위한 tcp 느린 시작
-  데이터를 한번에 전송하는 네이글(nagle) 알고리즘
-  tcp 편승(piggyback) 확인 응답(ack)를 위한 확인 응답 지연 알고리즘
-  TIME_WAIT 지연과 포트 고갈
#### TCP 커넥션 핸드쉐이크 지연
##### 핸드쉐이크 순서
1. 클라이언트 -> 서버) SYN 패킷 전송
2. 서버 -> 클라이언트) SYN + ACK 패킷 전송
3. 클라이언트 -> 서버) ACK 전송
> TCP 연결 생성시 3 Way Handshakce, TCP 연결 종료시 4 Way Handshake
보통의 경우 2번에서 (SYN+ACK)가 큰 크기를 갖는다. 크기가 작은 HTTP 트랜젝션은 50%이상의 시간을 TCP를 구성하는데 사용한다. 그래서 HTTP는 이미 존재하는 커넥션을 재활용 해야한다.
#### 확인 응답 지연
- TCP 통신은 세그먼트와 체크섬 확인 후 확인 응답을 전송한다.
- 확인 응답은 작은 크기를 가지기 때문에 데이터 패킷에 편승(piggyback)하여 전송한다.
- 편승하기 위해서 확인 응답을 바로 보내지 않고 TCP 스택에 넣고 잠시 기다리는 알고리즘이 있다 
- 해당 알고리즘에서 지연시간 발생
> piggyback은 "어부바"라는 뜻이다. 옛날에는 돼지를 어부바해서 팔았나보다.
#### TCP 느린 시작(slow start)
TCP는 커넥션 형성 초기에는 한번에 적은 수의 패킷만 전송할 수 있다. 확인 응답을 받으면서 한번에 전송할 수 있는 패킷의 수를 점차 늘려나간다. 처음에 패킷의 수를 제한하는 것을 **느린 시작(slow start)** 이라고 하고 가능한 패킷의 수를 늘려 나가는 것을 **혼잡 윈도우를 연다(opening the congestion window)** 라고한다.

#### 네이글(Nagle) 알고리즘과 TCP_NODELAY
작은 크기의 TCP 패킷을 계속 해서 전송하게 되면 성능이 저하된다. 그래서 네이글은 패킷을 최대 크기로 모아서 전송하는 알고리즘을 제시했다.
- 단점
  - 크기가 작은 패킷은 안 생길지도 모르는 다른 패킷이 생기는 것을 기다려야한다.
  - 확인 응답 지연과 함께 쓰일 경우 성능이 크게 저하된다.
- TCP_NODELAY
  - TCP_NODELAY 파라미터를 사용해서 네이글 알고리즘을 비활성화할 수 있다.

#### TIME_WAIT의 누적과 포트 고갈
TCP 커넥션을 종료하면 메모리의 제어 영역에 ip주소와 포트번호를 저장해놓는다. 저장된 정보를 가지고 종료된 수 일정 시간동안 저장된 주소와 포트에서 새로운 패킷이 도착했을 때 무시한다.

TIME_WAIT는 테스트 환경에서 주의해야하는데 보통의 테스트 환경은 ip를 고정하고 포트를 바꿔서 사용한다. 하지만 포트는 제한되어 있기 때문에 동일 포트로 전송하게 되는 경우 성능이 저하되어 측정된다.
### HTTP 커넥션 관리
- Connection Header
  - 클라이언트에서 서버로 데이터를 전송할 떄 중간에 다른 곳을 통해서 전달할 수 있다.
  - 이 때 Connection Header을 Hop-by-hop 전송을 사용할 수 있다.
  - 특정 위치에서 사용하고 삭제할 수 있다.
#### 순차적 트랜잭션 처리 지연
모든 트랜잭션에서 새로 커넥션을 생성하게 되면 긴 지연시간이 생기게된다. 그래서 HTTP 커넥션의 성능을 향상 시키기 위한 기술이 있다.
- 병렬(parallel) 트랜잭션: 여러개의 TCP 커넥션을 통한 동시 HTTP 요청
  - 클라이언트와 여러개개의 커넥션을 형성함으로 여러개의 트랜잭션을 병렬적으로 처리
  - 클라이언트의 대역폭이 좁은 경우 성능상 장점은 없다.
  - 총 걸리는 시간이 더 길어질 수도 있다.
- 지속(persistance) 커넥션: 커낵션 재활용
  - HTTP/1.1은 한번 연결된 커넥션을 일정시간 유지하여 동일한 곳에서 새로운 커넥션 생성을 방지해준다.
  - 커넥션을 유지하게 되면 튜닝되어있는 커넥션을 사용할 수 있다.
  - Keep-Alive
    - HTTP/1.0+에서 사용되었고 HTTP/1.1에서는 삭제되었다.
    - Connection 헤더에 Keep-Alive 를 넣어주면 사용가능.
    - keep-alive를 지원하는 멍청한 프록시가 있으면 클라이언트의 keep-alive요청을 서버로 전달하여 서버와 keep-alive를 형성. 하지만 멍청한 프록시가 kepp-alive를 모르기때문에 연결을 끊지 않아서 문제 발생
  - 규칙 
    - 클라이언트가 Connection:close 헤더를 포함해서 보냈으면 추가적인 요청을 보낼 수 없다.
    - 추가적인 요청을 보내지 않을 시에 Connection:close를 전송해야한다.
    - 요청의 정확힐 길이를 가지고 있어야 커넥션을 유지할 수 있다.
    - 클라이언트와 서버가 각각 지속 커넥션을 맺고 관리해야한다.
    - HTTP/1.1 기기는 Connection 헤더의 값과 상관ㅇ벗이 언제든지 커넥션을 끊을 수 있다.
    - 중간에 끊어진 커넥션을 복구할 수 있다.
    - 클라이언트의 지원 범위를 알고 있지 않으면 지속 커넥션을 형성하면 안된다.
    - 전체 응답을 받기전에 커넥션이 끊어지면 다시 요청을 보낼 수 있다. 
    - 서버의 과부하 방지를 위해 각 클라이언트 당 2개의 지속 커넥션을 유지한다.
- 파이프라인(pipelined) 커넥션: 공유 TCP 커넥션을 통해서 병렬 HTTP 요청
  - 지속 커넥션을 통해서 사용 가능
  - keep-alive의 성능 향상
  - 규칙
    - 형성된 커넥션이 지속 커넥션인지 확인 전까지 파이프라인 형성 x
    - http는 응답이 요청 순서와 같게 수신돼야한다. -> http 메시지는 정렬할 수 없다.
    - 커넥션이 끝어져도 완료되지 않은 요청이 파이프라인에 있다면 다시 보낼 수 있다.
    - 비멱등성(nonidempotent) 요청을 파이프라인을 통해 보내면 안된다.
- 다중(multiplexed) 커넥션: 요청과 응답들에 대한 중재
### 커넥션 끊기
TCP 커넥션은 입력과 출력으로 이루어져있다.
- close()를 사용하면 입출력 전체를 끊는다.
- shutdown()을 사용하면 입력 또는 출력을 끊는다. (절반끊기)
  - 예상치 못한 쓰기 에러를 방지하기위해 사용
  - 끊어진 서버 입력 채널에 데이터를 전송한 경우
    - 서버 운영체제는 TCP "connection reset by peer" 메시지를 클라이언트에 전송한다.
    - 클라이언트는 버퍼에 저장된 아직 읽지 않은 데이터를 모두 삭제한다.
#### 우아하게 커넥션 끊기
1. 출력 채널을 끊는다. -> 커넥션 리셋 위험 삭제
2. 입력 채널에 대해 주기적인 상태검사
3. 타임 아웃 내에 끊어지지 않으면 강제로 끊음