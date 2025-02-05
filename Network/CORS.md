## CORS
* **CORS** (Cross Origin Resource Sharing) 는 출처가 다른 곳의 리소스를 요청할 때 접근 권한을 부여하는 메커니즘이다.
* 이때 출처는 URL 뿐만 아니라 프로토콜과 포트까지 포함된다.
* 만약 클라이언트의 출처가 허용되지 않았다면 CORS 에러가 발생할 수 있다.

### CORS는 왜 필요할까?
* 과거에 **크로스 사이트 요청 위조 문제**가 있었다.
  * 피해자의 브라우저에서 다른 애플리케이션으로 가짜 클라이언트 요청을 전송하는 공격

### CORS는 어떻게 작동할까?
* 브라우저가 요청 메시지에 **Origin 헤더**와 응답 메시지의 **Access-Control-Allow-Origin 헤더를 비교**하여 CORS를 위반하는지 확인
* Origin에는 현재 요청하는 클라이언트의 출처(프로토콜, 포트, 도메인)가, Access-Control-Allow-Origin은 리소스 요청을 허용하는 출처가 작성됨
