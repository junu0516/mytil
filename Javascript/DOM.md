# DOM, CSSOM, Render Tree에 대해 알아보자

### 1. DOM(Documnet Object Model)

([참고](https://wit.nts-corp.com/2019/02/14/5522))

![DOM | PoiemaWeb](https://poiemaweb.com/img/HTMLElement.png)

- __`Node`__ : EventTarget 오브젝트를 상속하며, 이는 다시 말해 모든 __Node__ 는 이벤트가 발생할 수 있음을 의미

  - __`Document`__ , __`Element`__ , __`Text`__ 등은 모두 __Node__ 를 상속받았기 때문에, 마찬가지로 이벤트가 발생할 수 있는 요소임

- 브라우저는 HTML 파일을 읽어서 각각의 태그를 분석 후, 이를 자바스크립트 노드로 변환함

- 이를 바꿔 말하면, 마크업 언어를 브라우저가 하나씩 읽어들이면서 브라우저가 이해할 수 있는 __`DOM Tree`__ 로 변환한다고 표현

  - html 하위에 head와 body가 있고 head 하위에는 meta와 title 등이, body 하위에는 section과 span 등이 각각 있는 구조

    ```html
    <head>
        <meta charset="UTF-8">
        <title>Document</title>
    </head>
    <body>
        <section>
        <!-- 태그 선언 -->
        </section>
        <span>Hello World</span>
    </body>
    ```

    - html 가각의 태그들이 DOM Tree의 요소와 하나씩 맞물리는 것으로 이해하자!

      `

- __DOM = HTML 로 절대 착각하면 안됨__

  - HTML 문서가 유효하지 않더라도 보통 브라우저는 이를 올바르게 교정함

    ```html
    <html>
    Hello World
    </html>
    ```

    - 위의 html은 <head> 와 <body> 태그를 각각 빠뜨렸지만, 브라우저는 이를 교정하기 때문에 DOM Tree에는 최종적으로 올바른 노드 구조가 나타나게 될 것
    - __따라서 DOM은 굳이 얘기하면 유효한 형태의 HTML이라 할 수 있을 것__

  - 또한, Javascript 코드로 DOM을 조작하는 코드가 존재한다고 해서 html을 변경하는 것은 아님

    

- __브라우저에 보이는 것들이 DOM은 아님__

  - __`display : none`__ 과 같이 속성이 정의된 경우도 DOM Tree의 구조에 속하기 때문!

  - 뒤에서 설명하겠지만 브라우저에 보이게 되는 것들은 __`Render Tree`__ 로 표현

    

- __개발자 도구로 DOM을 완전히 볼 수 있는 것이 아님__
  - 크롬의 개발자 도구는 DOM의 근사치를 보여주지 100% 완벽한 DOM Tree를 보여주지는 않음
  - ::before, ::after 과 같은 가상의 요소는 개발자 도구에서 보이지만, DOM Tree에는 포함되지 않음

.

### 2. EventTarget

- [MDN 공식문서 참고](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget)

- DOM 인터페이스로, 클릭, 변화 등의 이벤트 적용 대상이 되는 오브젝트로 구현됨
- __`Element`__ , __`Document`__ , __`Window`__ 모두 이벤트 타겟이며, 위에서 설명했듯이 __`Node`__ 자체가 __`EventTarget`__ 을 상속받기 때문에 대부분의 요소는 모두 이벤트 적용 대상이 됨 

- 이벤트 타겟의 주요 메소드로 __`addEventListener()`__ , __`removeEventListener()`__ , __`dispatchEvent()`__ 등의 자바스크립트 메소드가 있음

  ```javascript
  var EventTarget = function() {
    this.listeners = {};
  };
  
  EventTarget.prototype.listeners = null;
  EventTarget.prototype.addEventListener = function(type, callback) {
    if (!(type in this.listeners)) {
      this.listeners[type] = [];
    }
    this.listeners[type].push(callback);
  };
  
  EventTarget.prototype.removeEventListener = function(type, callback) {
    if (!(type in this.listeners)) {
      return;
    }
    var stack = this.listeners[type];
    for (var i = 0, l = stack.length; i < l; i++) {
      if (stack[i] === callback){
        stack.splice(i, 1);
        return;
      }
    }
  };
  
  EventTarget.prototype.dispatchEvent = function(event) {
    if (!(event.type in this.listeners)) {
      return true;
    }
    var stack = this.listeners[event.type].slice();
  
    for (var i = 0, l = stack.length; i < l; i++) {
      stack[i].call(this, event);
    }
    return !event.defaultPrevented;
  };
  
  ```

  .

### 3. CSSOM(CSS Object Model)

`

![렌더링 트리 생성, 레이아웃 및 페인트 | Web | Google Developers](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/render-tree-construction.png?hl=ko)

- 브라우저가 html을 읽어 DOM을 생성하면, CSS를 병합하여 CSSOM을 생성(CSS 객체모델)
- 위의 CSSOM 그림을 간단히 해석하면, <body> 태그 요소에 대해 폰트 사이즈를 18px로 지정하면 하위 모든 노드에 대해서 동일한 스타일이 적용된다는 것을 의미
- DOM 과 CSSOM이 합쳐지면 최종적으로 브라우저에 표시될 __`Render Tree`__ 가 완성됨!
  - Render Tree 그림을 보면 DOM Tree와 달리 body만 들어가 있는 것을 볼 수 있음
  - HTML의 head 노드 하위는 사용자 눈에 보이지 않기 때문
  - 마찬가지로, __`display:none`__ 으로 설정된 노드 또한 Render Tree에 포함되지 않음

.

### 4. 랜더링 순서

`

![렌더 트리(Render Tree), CRP(Critical Rendering Path)](https://blog.kakaocdn.net/dn/TdeBl/btqMgsbm8Mb/8m46abGUg4K9cXMw1zi68K/img.png)

- 브라우저에 특정 url을 입력하게 되면 request/response -> loading -> scripting -> rendering -> layout -> painting 의 순서로 일어남
- 응답받은 html 문서를 로딩하여, DOM으로 변환하는 것이 scripting
- DOM과 CSSOM을 합쳐 Render Tree로 만드는 것이 rendering
- Render Tree를 참고하여 브라우저상의 위치 및 크기를 어떻게 보여줄 지 결정하는 것이 layout
-  좀 더 간단히 표현하면, DOM+CSSOM = Render Tree 의 과정은 __`Construction`__ 이 되며, Render Tree를 브라우저에 최종적으로 표시하는 과정은 __`Operation`__ 으로 나타낼 수 있음
- 위의 랜더링 순서를 통해 DOM, CSS 선언 등의 수가 적을 수록 Render Tree의 크기가 작아지기 때문에 속도가 더욱 빨라질 것임을 유추할 수 있음
- 따라서 프론트엔드 개발시 불필요한 <div> 나 쓸 데 없는 wrapping 용도의 요소를 선언하는 것을 지양해야 함!

