# 함수, 콜백함수, 클래스

> 자바스크립트 함수에 대해서 다시 한 번 정리해보자 ..  

#### 

### 1. 자바스크립트 function

- (정말 당연한 이야기지만) 반복적으로 사용되는 로직을 별도의 함수로 만들어서, 코드의 재사용성을 높이고자 사용하는 것

- __`function`__ 은 __`let`__ 이나 __`const`__ 와 같이 함수를 의미하는 자바스크립트 키워드임

- 함수를 선언하게 되면 함수의 코드블럭 __`{ }`__ 내에 선언된 코드들은 별도의 메모리 공간에 저장됨

  - 함수명은 함수가 위치한 메모리 공간을 가리키는 레퍼런스로 별도의 공간에 위치함
  - 변수 - 객체 의 할당과 유사하다고 보면 됨

- 아래와 같이 변수에 함수를 할당하게 되면?

  - add 함수를 가리키는 레퍼런스로, 변수 fn이 또 다른 메모리 공간에 위치하게 됨
  - 따라서 변수만 가지고 함수를 호출할 수 있음

  ```javascript
  function add(a,b){
      return a+b;
  }
  const fn = add; //함수를 변수에 할당
  const result = fn(1,2);
  console.log(result); //add(1,2)와 동일한 값이 출력됨을 확인
  ```

- 아래와 같이 함수를 파라미터로 전달할 수도 있음

  ```javascript
  function outerFn(innerFn){
      const result = innerFn();
      console.log(result);
  }
  
  outerFn(function(){
     let a = 2;
     let b = 3;
     return a+b;
  }); //5를 리턴하는 함수를 파라미터로 받아서, 이를 콘솔에 출력
  ```

  

####  

### 2. 클래스

- 

