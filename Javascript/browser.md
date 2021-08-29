# 브라우저 구조

### 1. Window 오브젝트의 구성요소

- 브라우저에서 현재 열려 있는 전체적인 창에 해당하는 오브젝트를 의미([공식문서](https://developer.mozilla.org/ko/docs/Web/API/Window))

- Window 안에, DOM, BOM, Javascript 등의 구성요소가 있는 것

- __DOM__ (Document Object Model)

  - __Document__  : 창에서 HTML 요소가 표기되는 부분
  - 새로운 요소를 추가하고 조작하는 것이 가능

  ```javascript
  console.log(window); //window object 출력
  console.log(this); //global 변수로 동일하게 window 객체 출력 가능
  
  console.log(window.innerWidth);
  console.log(innerWidth);
  ```

- __BOM__ (Browser Object Model) 

  - 브라우저 관련 오브젝트로 __Navigator__ , __Storage__ 등이 여기에 속함
  - __Navigator__ : 브라우저 자체 관련 정보가 담기는 부분으로, appCodeName, clipboard, connection, bluetooth 등의 요소가 여기에 속함

- __Javascript__

  - Array, Map 등의 자바스크립트 요소

.

### 2. Window 사이즈 표기와 브라우저 좌표

- window.screen : 모니터 사이즈

- window.outer : 브라우저 크기

- window.inner : 윈도우 크기

- documentElement.clientWidth : 스크롤바를 제외한 페이지 영역 크기

  .

- client 좌표와 page 좌표의 차이

  - client 좌표는 현재 보이는 브라우저 화면(클라이언트 영역) 내에서의 좌표를 의미
  - page 좌표는 스크롤 화면을 포함한 전체 페이지 내에서의 좌표를 의미

  ```javascript
  //class name이 box인 div 박스가 존재한다고 가정
  const box = document.querySelector(".box"); //div 요소 선택
  
  box.addEventListener('click',function(event){
      const rect = box.getBoundingClientRect();
      console.log(`client: ${event.clientX}, ${event.clientY}`); //현재 페이지 기준
      console.log(`page: ${event.pageX}m ${event.pageY}`); //전체 브라우저 페이지 상위 기준
  })
  ```

  - 스크롤을 움직이면서 같은 곳을 클릭해보면, clientX, clientY의 값은 스크롤을 내릴 때마다 변함
  - 반면, pageX, pageY는 스크롤을 무시한 전체 페이지 기준이기 때문에 값이 변하지 않음

