# Event

![javascript _Event](https://media.vlpt.us/images/denmark-choco/post/47859325-15f1-4240-bbaa-9fcc83c1cc8f/%E1%84%8B%E1%85%B5%E1%84%87%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B3.jpg)

`

### 1. Event의 개념

- mouse click, keyboard, page loading 등의 모든 일을 포괄하는 개념
- [MDN Document](https://developer.mozilla.org/ko/docs/Web/Events) 에서 Event의 종류를 확인하도록 하자 ...
- __Event Handling__ : 이벤트 발생 시 개발자가 원하는 동작이 이루어지도록 선언
- __Event Handler__ : 이벤트 발생시 원하는 동작을 실행할 로직이 정의된 부분

`

### 2. Event 제어하기

- __EventTarget.addEventListener()__

  - ```tex
    Registers an event handler of a specific event type on the EventTarget
    ```

  - 특정 요소에 __`EventListener`__  를 등록하여 __`EventHandler`__ 로 기능하도록 함

  ```html
  <button id="btn">Event</button>
  <script>
      //Button 요소에 클릭 이벤트를 등록
      let btn = document.querySelector("#btn");
      btn.addEventListener("click",()=>{
          console.log("button clicked");
      });
  </script>
  ```

- __EventTarget.removeEventListener()__

  - 위와 반대로 Event를 제거하는 것

- __EventTarget.dispatchEvent()__

  - ```
    Dispatches an event to this EventTarget
    ```

  - 쉽게 말해 Event를 인공적으로 발생시키는 것으로, 아래 예시는 미리 정의한 click 이벤트 발생 시 실행시킬 동작을 인위적으로 실행시킨 것

  ```javascript
  //위의 상황에서 click 이벤트를 인위적으로 호출시키는 것으로, 콘솔에 동일하게 "button clicked"가 출력되는 것을 확인할 수 있음
  btn.dispatchEvent(new Event("click"));
  ```

`

### 3. Event Bubbling & Capturing

![Deep dive into JavaScript event bubbling and capturing - LogRocket Blog](https://blog.logrocket.com/wp-content/uploads/2021/07/event-capturing-bubbling-sequence-diagram.png)

`

- __Event Bubbling__

  - 특정 요소에서 이벤트가 발생했을 때, 이벤트가 상위 요소로 계속해서 전달되는 특성

  - __`DOM Tree`__ 란 단어에서 알 수 있듯이  HTML 태그들은 트리 구조로 랜더링 되기 때문에, 위의 그림처럼 어떤 하위 요소에서의 이벤트가 트리 구조를 따라 상위 요소로 전달되는 것

    ```html
    <div id="outer">
    	<div id="inner">
        	<button id="btn">button</button>
         </div>
    </div>
    <script>
    	let btn = document.querySelector("#btn");
        let outer = document.querySelector("#outer");
        let inner = document.querySelector("#inner");
        
        btn.addEventListener("click",()=>{
            console.log("clicked");
        });
        outer.addEventListener("click",()=>{
            console.log("outer box clicked");
        });
        inner.addEventListener("click",()=>{
            console.log("inner box clicked");
        });
    </script>
    ```

  - 위와 같은 경우에, 제일 하위에 있는 __`<button>`__ 태그에 등록된 이벤트가 실행될 경우, 콘솔에는 해당 태그의 상위 요소(여기서는 inner, outer __`<div>`__) 에 등록된 이벤트도 동시에 실행될 것

`

- __Event Capturing__

  - __`Event Bubbling`__ 과 반대 방향으로 이벤트가 전달되는 특성

  - 위와 동일한 자바스크립트 코드에서, 반대 방향으로 이벤트를 전달할 수 있음

    - __`addEventListener(event,function,useCapture)`__ 에서 세 번째 인자의 값으로 __`catpure:true`__ 를 적용
    
    ```javascript
    let divs = document.querySelectorAll("div");
    divs.forEach(function(div){
        div.addEventListener("click",function(){
            alert("clicked");
        },true);
    });
    ```

`

- __event.stopPropogation()__ 

  - __`event.stopPropagation()`__ 

    ```javascript
    function cancel(event){
        event.stopPropation();
    }
    ```

    - 만일 __`Event Bubbling`__ 이 발생하는 상황이라면, 최하위 요소에서만 이벤트가 발생하고 상위요소로는 전달되지 않음
    - 하지만 무턱대고 쓰면, 단순히 이벤트를 한 번만 실행하고 얄짤없이 취소하기 때문에(...) 규모가 큰 프로젝트의 경우에는 디버깅이 어려워질 수 있음!
    - 이럴 때는, 취소를 선언하기 보다는, 아래와 같이 원하는 타겟(__`event.target`__)에서만 이벤트가 발생하도록 변경하는 것이 나음

    ```javascript
    <div id="outer">
    	<div id="inner">
        	<button id="btn">button</button>
         </div>
    </div>
    <script>
    	let btn = document.querySelector("#btn");
        let outer = document.querySelector("#outer");
        let inner = document.querySelector("#inner");
        
        btn.addEventListener("click",()=>{
            console.log("clicked");
        });
    
    	//타겟이 다를 경우에는 그냥 return
        outer.addEventListener("click",event=>{
            if(event.target !== event.currentTarget){
                return;
            }
        });
        inner.addEventListener("click",event=>{
            if(event.target !== event.currentTarget){
                return;
            }
        });
    </script>
    ```

`

### 4. 브라우저 동작 취소하기

`

```html
<div class="box">
    <input type="checkbox" class="checkbox">
</div>
<script>
    const checkbox = document.querySelector(".checkbox");
    checkbox.addEventListener("click",event=>{
        console.log("checkbox clicked");
        event.preventDefault(); //브라우저에서 발생하는 동작 취소
    });

    document.addEventListener("wheel",event=>{
        console.log("scrolled!");
        event.preventDefault();
    });

</script>
```

- click 이벤트를 정의하는 로직에서 __`event.preventDefault()`__ 를 선언하였음

> The event interface's preventDefault() method tells the user agent that if the vent does not get explicitly handled, its defualt action should not be taken as it normally would be.

- MDN에 나온 위의 정의대로면, 위의 자바스크립트 코드에서 __`checkbox`__ 에 선언된 __`default action`__ , 즉 클릭시 체크박스에 체크가 표시되는 이벤트는 취소되는 것을 의미 

  (실제로 콘솔은 찍히지만, 브라우저에서 체크는 찍히지 않는 상황 발생)  

  `

- 단, __`Passive Event Listener`__ 의 경우에는 preventDefault가 불가능

```javascript
document.addEventListener("wheel",event=>{
    console.log("scrolled!");
    event.preventDefault();
});
```

- 위의 자바스크립트 코드를 적용하면, 콘솔에 아래와 같은 문구가 뜨게 됨

```tex
[Intervention] Unable to preventDefault inside passive event listener due to target being treated as passive. See <URL>
```

- 말 그대로, 스크롤링과 같은 이벤트에는 적용이 불가능한 것
- 직관적으로 생각해보면, 콘솔에 스크롤링과 같은 이벤트를 수행하는데 단순 콘솔 출력이 아닌 좀 더 긴 로직을 실행한다고 했을 때, 이를 기다렸다가 마지막에 스크롤링 이벤트를 수행 혹은 취소하는 것이 말이 안됨
- 따라서 이런 식으로 빠르게 동작해야 할 필요가 있는 __`Passive Event`__ 에 __`preventDefault()`__ 를 적용하려 들면 브라우저는 이를 자연스레 무시(...)

`

*그렇다면 passive event를 취소하고 싶다면..?*

```javascript
document.addEventListener("wheel",event=>{
    console.log("scrolled!");
    event.preventDefault();
},{passive:false});
```

> A boolean value that, if `true`, indicates that the function specified by `listener` will never call [`preventDefault()`](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault). If a passive listener does call `preventDefault()`, the user agent will do nothing other than generate a console warning. See [Improving scrolling performance with passive listeners](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#improving_scrolling_performance_with_passive_listeners) to learn more.

- passive : false, 즉 해당 요소를 passive가 아닌 active 요소로 바꿔서, preventDefault()가 먹히도록 하는 것
- 실제로 위의 코드를 실행하면 스크롤링이 되지 않음

`

### 5. Event Delegation

`

- 아래와 같은 __`ul`__ 태그가 있다고 가정하자

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Event 취소</title>
    <style>
        .hilighted{
            background-color: yellow;
        }
    </style>
</head>
<body>
    <ul>
        <li>one</li>
        <li>two</li>
        <li>three</li>
        <li>four</li>
    </ul>
</body>
</html>
```

- 여기서 모든 __`li`__ 태그에 동일한 이벤트를 걸고 싶다면?

  - __1) Bad Case__

  ```javascript
  const lis = document.querySelectorAll("li");
  lis.forEach(li=>{
      li.addEventListener("click",event=>{
          console.log("li selected");
      });
  });
  ```

  - __2) Better Case__

  ```javascript
  const ul = document.querySelector("ul");
  ul.addEventListener("click",event=>{
      if(event.target.tagName == "LI"){
          if(event.target.classList.contains("hilighted")){
              event.target.classList.remove("hilighted");
          }else{
              event.target.classList.add("hilighted");
          }
      }
  });
  ```

- 아래와 같은 상황이 __`Event Delegation`__ 의 한 예로, 동일한 하위 요소에 일일히 이벤트를 적용하지 않고 상위 요소 하나에만 이벤트를 적용한 후, 하위에서 발생한 이벤트를 감지하는 것을 의미
- __`Event Bubbling`__ 을 응용한 것으로, 위의 경우에는 __`ul`__ 에 적용되는 클릭 이벤트도 감지하기 때문에, __`tagName`__ 비교하는 조건문을 하나 추가해서 __`li`__ 에 적용되는 이벤트만 감지하도록 하였음

