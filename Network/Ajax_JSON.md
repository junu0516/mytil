# Ajax 통신과 JSON

> 회사에서 짜놓은 스프링부트 프로젝트들을 보면 대부분의 요청을 Ajax를 통해 구현해놓았다. 학원에서는 어렴풋이 댓글과 같은 부분적인 페이지 업데이트를 위해 사용하는 정도로 알고 있었지만, 비동기식 전송이라는 특징상 Ajax 요청은 더욱 다양한 상황에서 활용되는 것 같다. 
>
> 또한, 동일 출처 정책과 같은 것이 있는 것을 몰라서 Ajax 요청을 다른 서버로 보내놓고 바보같이 csrf 설정만 계속 건드려보는 삽질을 하기도 했다. 이번 기회에 Ajax에 대해 배운 것들을 다시 정리해보았다.



### 비동기 통신이란?

![img](https://blog.kakaocdn.net/dn/CGKRX/btqSLHV9qTm/7IoRWNojLEbeDjcAIRhpSK/img.png)

- __동기식 전송__ 이 요청과 응답이 동시에 일어나는 방식을 말하면, __`비동기식 전송`__ 은 요청과 응답이 동시에 일어나지 않고, 응답이 오지 않아도 다른 요청을 보내고 응답을 기다리는 것이 가능한 방식을 말함
- 결과를 바로 알 수는 없지만, 여러 건의 통신을 처리할 경우 작업 간의 순서를 일일히 고려하지 않아도 됨
  - 동기식 전송은, 일단 요청을 보내면 응답이 올 때까지 다른 작업을 처리하지 못하는 단점이 있음
  - 따라서 비동기식 전송은 동기식 전송에 비해 자원을 좀 더 효율적으로 사용할 수 있도록 해주는 것!



### Ajax(Asynchronous Javascript And XML) 통신이란?

![AJAX](https://webwox.files.wordpress.com/2011/08/ajaxsequencev2.gif?w=477&h=267)



- 자바스크립트를 이용하여 비동기식으로 서버와 클라이언트가 서로 데이터를 주고받는 통신을 말함
  - 주고받을 수 있는 데이터는 XML 외에도 JSON, text, html, csv 등 다양한 형식이 있음
  - 이름에 굳이 XML이 붙은 것은, Ajax 등장 초기에는 XML로만 데이터를 주고받았기 때문
- Ajax 통신을 할 경우 전체 페이지를 갱신하지 않고 __일부분만을 업데이트__ 하여 시간과 자원을 절약할 수 있음
  - 가령, 댓글을 업데이트하기 위해서는 전체 페이지의 리디렉션보다는 댓글 부분만 업데이트하는 것이 효율적일 것
  - 사용자 입장에서는 더욱 풍부한 UI 경험을 제공받을 수 있음을 의미
- 하지만 무분별한 비동기 통신은 디버깅과 같은 __히스토리 관리에 그만큼 어려움__ 을 줄 수 있음
- 또한, 응답없이 여러 건의 요청을 동시다발적으로 보내기 때문에 __서버에의 부하가 증가할 수도 있음__



- __Cross Domain Issue__ 관련 문제 또한 존재

  ![javascript ajax 크로스도메인 요청-CORS](https://img1.daumcdn.net/thumb/R1280x0.fpng/?fname=http://t1.daumcdn.net/brunch/service/user/1pJY/image/OhRUYAA_taKZtLVo5BMCpomOqUo.png)

  - 만일 크롬에서 Ajax로 다른 도메인으로 요청을 보내면 아래와 같은 에러 메시지가 나타날 것

  ```
  No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin '****' is therefore not allowed access.
  ```

  - 이는 자바스크립트의 __Same-Origin Policy__ 규칙에 따라 __`XMLHttpReqeust`__ 전송시, 같은 출처(Same Origin)의 페이지에만 접근할 수 있기 때문
  - 여기서 같은 출처는 __동일한 프로토콜, 호스트명, 포트__ 의 세 조건을 충족시키는 페이지를 말함
  - 따라서 Ajax 요청시 다른 도메인으로 요청을 보낼 수 없는 것
    - 웹브라우저에서 다른 도메인으로의 요청을 허용하도록 설정하거나, 별도의 플러그인을 설치
    - 혹은,  __`JSONP`__ 방식으로 요청하기도 함
      - css, js 파일 등의 리소서들은 보통 Same-Origin Policy에 영향을 받지 않기 때문에, json과 같은 리소스 형식으로 바꿔 GET방식으로 API를 요청하는 것

  

- 오늘날에는 여러 도메인을 오가며 요청&응답을 주고받는 경우가 많기 때문에 W3C에서는 __`CORS(Cross-Origin Resource Sharing)`__ 이라는 새로운 정책을 내놓았음

  - 서버에서 외부 요청을 허용하는 설정을 할 경우에는 다른 도메인으로도 Ajax 요청이 가능해지는 것

  - 응답 헤더로 아래의 요소를 추가

    ```
    Access-Control-Allow-Origin:*
    Access-Control-Allow-Methods:GET,POST,PUT,DELETE,OPTIONS
    Access-Control-Max-Age:3600
    Access-Control-Allow-Headers:Origin.Accept.X-Requested-With,Content-Type,Access-Control-Request-Method,Access-Control-Request-Headers.Authorization
    ```

    - 좀 더 자세한건 [Spring에서 처리하기](https://spring.io/guides/gs/rest-service-cors/) 를 참고하자

.

### Ajax 구현하기(Vanila Javascript)

> Jquery를 사용하는 방식은 널려있기 때문에 여기서는 Vanila Javascript로 구현한 코드를 정리해보았다!

```javascript
function ajaxTest(){
	let ajaxRequest = new XMLHttpRequest(); //Ajax Request 객체 생성
    let jsonData = {
        key : "value"
    }
    
    ajaxRequest.onreadystatechange = function(){
        if(ajaxRequest.readyState === ajaxRequest.DONE){
            if(ajaxRequest.status === 200 || ajaxRequest.status ===201){
                //응답코드가 200,201인 경우에는 성공 로직 처리를 명시
            }else{
                //400,500번대 에러가 났다면 에러 로직 처리를 명시
            }
        }
    };
    
    ajaxRequest.open('POST','/url'); //요청 보낼 url와 메소드 명시
    ajaxRequest.setRequestHeader('Content-Type','application/json'); //헤더 설정
    ajaxRequest.send(JSON.stringify(jsonData)); //여기서는 데이터를 문자열로 전송 
}
```



- 만일 __`<form/>`__ 태그를 활용하고자 할 경우에는 __`FormData`__ 객체를 만들어 아래와 같이 전송

  ```javascript
  let formData = new FormData();
  formData.append('key','value');
  ....
  ajaxRequest.send(formData); //formData 전송
  ```

  

### JSON(Javascript Object Notation) 이란?

![Will They Blend? Experiments in Data &amp; Tool Blending. Today: XML meets JSON  | KNIME](https://www.knime.com/sites/default/files/styles/inline_large/public/inline-images/xml-json.png?itok=JTpUrKjv)

- 자바스크립트에서 Ajax 요청시 생성하는 객체의 이름이 __`XMLHttpRequest`__ 인 것에서 알 수 있듯, __`XML(Extensible Markup Language)`__ 은 사실 표면적으로 W3C의 표준 데이터 양식임

  - 마크업 언어의 형식을 따르기 때문에 <> 태그를 사용하지만, 단순히 데이터의 저장과 전달 목적으로만 존재하며 HTML과 달리 데이터를 보여주는 것은 아님
  - 하지만 태그를 포함하기 때문에 다른 데이터 양식과 비교했을 때, 데이터의 크기가 상대적으로 큼
  - 따라서 용량이 큰 단점을 보완하고자 상대적으로 가벼운 __`JSON`__ 형식을 사용하는 것

  

- JSON은 이름 그대로 자바스크립트 객체 표현 양식을 따르기 때문에 __"Key" : "Value"__ 의 형식을 따르며, 그만큼 가볍고 전송 속도가 더욱 빠른 이점이 있음

  ```json
  {
      "Countries":[
          {
              "Country":"Korea",
              "Capital":"Seoul"
          },
          {
              "Country":"Japan",
              "Capital":"Tokyo"
          },
          {
              "Country":"China",
              "Capital":"Beijing"
          }
      ]
  }
  ```

  - null, number, string, arry, object, boolean 등의 형식을 사용할 수 있고 []를 통해 순서가 있는 배열을 선언할 수도 있음
    - 단 주석은 지원하지 않으며 중괄호를 제대로 닫지 않는 등의 사소한 문법적인 실수로 전체 JSON의 오류를 초래할 수 있는 위험이 있음

  -  JSON 형식은 자바스크립트 객체로 손쉽게 변환(역직렬화)할 수 있으며, 자바스크립트 외에도 자바 등의 다른 프로그래밍 언어를 통해서도 JSON을 만들 수 있음

    - 이는 곧 JSON이 자바스크립트 객체의 형식만을 따를 뿐, 특정 언어의 문법에 종속되는 것이 아닌 것

    ![img](https://blog.kakaocdn.net/dn/bgzz2k/btqSTIM7KYy/q4dDknL3zKlKaqWL6vJuM0/img.png)

  - 대부분의 언어는 자바의 __`GSON`__ 과 같이, JSON 포맷의 데이터를 다룰 수 있는 라이브러리를 제공함

    - 자바의 경우 GSON 라이브러리의 __`toJson()`__ 을 통해 List나 Map 등의 객체를 JSON 형식의 데이터로 변환 

    
