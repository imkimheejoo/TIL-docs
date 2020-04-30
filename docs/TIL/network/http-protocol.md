# http-protocol

## Http Protocol 이란?
- 브라우저와 서버간에 데이터를 송/수신하기 위한 프로토콜
- http는 stateless protocol
    - stateless : 데이터를 주고 받기 위한 요청이 서로 독립적으로 관리
    - 각 요청간에 관련이 없음
    - 일반적으로 TCP/IP 통신 위에서 동작
    - 기본 port는 80

## HTTP 통신 방법
- http 프로토콜로 데이터를 주고받기 위해선 HTTP Request와 Http Response 가 필요
- 브라우저 -> 서버 : Http Request
- 서버 -> 브라우저 : Http Response
- HttpRequest를 보내는 방법은 URL을 이용
```text
http://localhost:8080/article/1
```
- http : protocol의 종류
- localhost : 호스트 이름 (도메인을 설정하면 도메인주소가 나옴 ex: www.naver.com)
- 8080 : port 번호
- /article/1 : 요청한 데이터에 대한 정보

### HTTP 특징
- 비연결 지향(Connectionless)
    - 클라이언트가 특정 동작을 수행하는 요청 서버에 보내고, 서버가 클라이언트에 요청에 대한 응답을 보내면 바로 연결을 끊음
- 무상태(Stateless)
    - 연결을 끊는 순간 클라이언트와 서버의 통신은 끝나며 상태 정보를 유지 X
- 장점
    - 불특정 다수를 대상으로 하는 서비스에 좋음.. 왜져..?
    - 클라이언트와 서버가 계속 연결된 형태가 아니기 때문에 클라이언트와 서버 간의 최대 연결 수보다 훨씬 많은 요청과 응답을 처리 가능 ?????
- 단점
    - stateless 이기 때문에 이전 요청정보를 알 수 없
    - 이전 요청에 대한 정보를 유지하기 위해서 Cookie 등장! (new!)

## HTTP 요청 메서드
- 데이터에 특정 동작을 수행하고 싶을 때 사용하는 방법
- GET : 기존 데이터를 요청(조회?)
- POST : 새로운 데이터 생성요청
- PUT : 기존 데이터의 전체적인 변경요청
    - PATCH : 기존 데이터의 일부만 변경요청
- DELETE : 기존 데이터의 삭제요청
- HEAD : 서버 헤더 정보 요청 (Response body X)
- OPTIONS : 서버 옵션들을 확인하는 요청 (CORS에서 사용)

## HTTP 상태코드
- URL이 서버에 관련된 데이터 정보를 담았다면, HTTP 상태코드는 서버한테 요청한 데이터에 대한 응답정보
- front에서 상태코드로 성공로직,에러처리로직 등을 분기가능
- `2xx` : 성공
- `3xx` : 리다이렉션
- `4xx` : 클라이언트 에러
    - 유효하지 않은 자원을 요청했거나(404), 사용자의 권한으로 실행할 수 없는 요청(403)
- `5xx` : 서버 에러

### 참고
- [최고의 캡틴판교님 블로그](https://joshua1988.github.io/web-development/http-part1/)
- [Heeee's blog](https://gmlwjd9405.github.io/2019/04/17/what-is-http-protocol.html)


    