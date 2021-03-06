# 기본 인증
- [기본 인증](#기본-인증)
  - [인증](#인증)
    - [HTTP의 인증 요구/응답 프레임워크](#http의-인증-요구응답-프레임워크)
      - [순서](#순서)
    - [인증 프로토콜과 헤더](#인증-프로토콜과-헤더)
    - [보안 영역](#보안-영역)
  - [기본 인증](#기본-인증-1)
    - [Base-64 사용자 이름/비밀번호 인코딩](#base-64-사용자-이름비밀번호-인코딩)
    - [프록시 인증](#프록시-인증)
  - [기본 인증의 보안 결함](#기본-인증의-보안-결함)
## 인증
### HTTP의 인증 요구/응답 프레임워크
- HTTP는 사용자 인증을 위한 자체 인증 요구/응답 프레임워크를 제공한다.
#### 순서
1. 요청
2. 로그인 요구
3. 로그인 요청
4. 응답

### 인증 프로토콜과 헤더
- 다른 인증 프로토콜에 맞추어 확장할 수 있는 프레임워크를 제공
- 인증 포로토콜은 HTTP 인증 헤더에 기술
- 기본 인증과 다이제스트 인증 지원
- 요즘에는 OAuth 사용
- 헤더
  - WWW-Authentication
    - 서버 -> 클라이언트 
   - 401 Unathorized
    - 어디서 어떻게 인증할지 
  - Authorization
    - 클라이언트 -> 서버
    - 인증 데이터 
  - Authentication-info
    - 서버 -> 클라이언트
    - 인증 세션에 관한 추가정보 기술

### 보안 영역
- 웹 서버는 기밀 문서를 보안 영역(realm) 그룹으로 나눈다
- 보안 영역는 각자 다른 사용자 권한을 요구한다.

## 기본 인증
- 가장 잘 알려진 HTTP 인증 규약
- 웹 서버는 요청을 거부하고 로그인 정보를 요구할 수 있다.(401 UnAuthenticated)

### Base-64 사용자 이름/비밀번호 인코딩
- HTTP 기본인증은 사용자 이름과 비밀번호를 콜롱느로 이어서 함침
- base-64 인코딩
- 사용자 이름과 비밀번호 노출을 막는다.

### 프록시 인증
- 중개 프록시 서버를 통해 인증 할 수 있다.

## 기본 인증의 보안 결함
- base-64는 쉽게 풀 수 있다.
  - 모든 트랜잭션을 SSL 암호화 채널을 통해 보내야한다.
- 복잡한 방식으로 인코딩했더라고 복제해서 사용할 수 있다.
- Man In the Middle 공격 가능