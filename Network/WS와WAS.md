# 웹서버(WS)와 웹어플리케이션서버(WAS)

![How To Setup Tomcat in Linux Mint – Truelogic Blog](https://lh3.googleusercontent.com/proxy/mxxE2UoE3I90GzmQTzRody7dOEZqlO0FuU7B162c26M6c69SwNQTgEQdp9injwLKG5v1ei6VhDUtppP9rXEimiwn_HPn2u07w6u-mf4e5Q8UzZMuXipIcFFHhkA5D09g0mG1J476F6s_XfT3WdvykqTFeX6SdRM)

### 웹서버(WS)

- __클라이언트로부터 http 요청을 받아, 응답으로 HTML 문서와 같은 웹페이지를 반환하는 프로그램__
  - 웹페이지에는 이미지, CSS, JS 등의 여러 정적인 리소스가 포함되어 있음
- __클라이언트로부터 콘텐츨르 전달받는 것도 웹서버의 기능이라 할 수 있음__
  - 주로 파일 업로드나, 클라이언트에서 제출한 form을 수신하는 경우가 이에 속함
- 정적인 결과물만을 제공하기 때문에 후술할 __`WAS`__ 에 비해 처리 속도가 빠르지만, 제공되는 서비스가 그만큼 한정적임
  - 글의 추가 및 수정 등과 같은 여러 동적인 작업이 수반될수록 처리가 번거로움
- __`Apache`__ , __`nginx`__ 등의 다양한 웹서버가 존재함
  - __Apache__ : 확장성이 높으며, __`모듈화`__ 를 통해 여러 기능을 덧붙일 수 있음
    - 초기에는 PHP+MySql이 결합된 APM이 흔히 쓰였음
    - __`JSP`__ 의 경우에도 Apache의 __`Tomcat`__ 과 연동하여 돌릴 수 있음
