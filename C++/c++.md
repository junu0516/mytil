# [방송통신대 강의노트] C++ 프로그래밍

[TOC]

<br>

## 1.  C++ 언어 기초 (1)

### 1-1 . C++ 파일과 프로그램 빌드

<img src="https://static.packt-cdn.com/products/9781789801491/graphics/C11557_01_02.jpg" width = "300" height="400">

- C언어에 객체지향, 일반화, 예외처리 등의 개념을 확장시킨 것 
- __C++ 소스 프로그램__ 의 확장자로는 __`.cpp`__ , __`.cxx`__ , __`.C`__ 등을 사용
- __C++ 헤더파일__ : __`#include`__ 키워드 선언과 함께 소스 프로그램 파일에 삽입되어 컴파일되는 파일
  - 클래스, 매크로, 전역변수, 상수 등 여러 소스 파일에 공통적으로 선언되는 내용을 담고 있음
  - 확장자로 __`.h`__ 를 사용
- 헤더 파일을 포함한 소스 프로그램 파일은 __목적 파일(Object File)__ 로 컴파일되며, 이것이 최종적으로 __`.exe`__ 와 같은 __실행 프로그램 파일(Executable File)로__ __빌드__ 되는 것 (위 그림 참조)

<br>

### 1-2. C++ 코드 작성 예시

```c++
#include <iostream> //선행처리기 지시어

int main(){
    
    int a; //정수형 변수 선언
    char str[100]; //배열 선언
    
    //표준 입력 스트림
    std::cin >> a >> str; //공백을 기준으로 a, str을 순서대로 입력
    
    //표준 출력 스트림으로 문장 출력
    std::out << "Hello World"
        	 << std::endl;
    return 0;
}
```

- __선행 처리__ : # 로 시작하여, 컴파일 전 소스 프로그램 가공 시 컴파일러가 번역할 소스프로그램으 만드는 것을 말함
  - __`#include`__ , __`#define`__ , __`#undef`__ , __`#if`__ , __`#ifdef`__ , __`#ifndef`__  등의 지시어가 있음
- __`std::cout`__ 은, 표준 출력 스트림 객체로 데이터를 문자열로 변환하여 콘솔에 출력함
  - __`<<`__ 은 출력 연산자를 의미하며, 위의 코드에서 __`std::endl`__ 은 end of line, 즉 개행의 종료를 선언한다는 의미임
- __`std::cin`__ 은 반대로 표준 입력 스트림 객체이며, 출력 연산자와 반대로 __`>>`__ 연산자를 사용함

<br>

### 1-3 명칭공간(namespace)

- 특정한 이름들이 인식되는 프로그램의 부분을 말하며, 동일한 명칭이라도 서로 다른 명칭공간에 정의되어 있다면 별개의 것으로 구분함
- 여러 프로그래머가 협업시 각자의 필요에 따라 독립적으로 명칭공간을 만들어 사용할 수 있음
- __전역 명칭공간__ : 특정 명칭공간에 속하지 않는 기본 명칭공간을 의미함
- __std 명칭공간__ : 표준 C++ 라이브러리 명칭들이 정의된 명칭공간을 의미함
- __`using`__ 키워드를 이용하여 특정 명칭공간 혹은 명칭공간 내 특정 이름을 자주 사용하는 경우, 명칭공간의 지정을 간소화할 수도 있음

```c++
#include <iostream>

//std 명칭공간 지정을 간소화하였음
// std::cout, std::endl 대신, 그냥 cout, endl만 선언해도 되는 것
using namespace std;

//변수명은 모두 n으로 동일하지만, 명칭공간이 모두 다르기 때문에 별개의 것으로 취급
namespace myNameSpace1 {int n=10;}
namespace myNameSpace2 {int n=20;}
int n=30;

int main(){
    int n=40;
    // myNameSpace1 명칭공간에 선언된 n
    cout << myNameSpace1::n<< endl;
    // myNameSpace2 명칭공간에 선언된 n
    cout << myNameSpace2::n<< std::endl;
    //전역 명칭공간에 선언된 n
    cout << ::n<< endl;
    //함수 내 지역변수로 선언된 n
    cout << n<< endl;
    
	return 0;
}
```

<br>

## 2. C++ 언어 기초(2)

### 2-1 키워드와 식별자

- __키워드(keyword)__ : 미리 용도가 정해진 단어를 의미하며, 정해진 용도로만 반드시 사용해야 함
  - __`using namespace`__ , __`return`__ 등의 것들이 키워드임
- __식별자(identifie)__ : 변수, 함수, 클래스 등 여러 대상을 구분하기 위해 만든 이름을 말함
  - 쉽게 말해 변수명, 함수명, 클래스명 등의 이름을 식별자라고 하는 것
  - 첫 글자는 숫자가 아닌 문자를 사용하도록 하며, 이후에는 숫자와 문자를 모두 사용
  - 길이 제한은 없으나 특수문자를 포함하면 안되며, __키워드는 식별자로 사용할 수 없음__

<br>

### 2-2 C++의 자료형

- 기본 자료형
  - __정수 자료형__ : char, int, short, long, bool 등..
    - 고정소수점(fixed-point) 방식의 숫자 표현
    - 연산시 정해진 범위를 벗어나는 오버플로우가 발생하지 않도록 주의
  - __실수 자료형__ : float, double, long double 등..
    - 부동소수점(floating-point) 방식의 숫자 표현
    - float은 4바이트, double은 8바이트 크기의 자료형
- 복합 자료형 
  - 열, 구조체, 클래스, 열거형(enum) 등
  - 포인터, 참조

- __정수형 리터럴 표현__
  - 정수형 리터럴 표현시, 숫자를 표현하는 문자와 부호만으로 표현함
  - 0~9까지의 숫자와 10~15에 해당되는 16진수 표현을 위한 문자 a~f, A~F를 사용
  - 접두사를 이용해서 정수형 리터럴을 표현할 수 있음(접두사가 없으면 10진수 취급)
    - 159 : 10진수로 표현된 int
    - **<u>0b</u>**10011111 : 2진수로 표현된 int
    - **<u>0</u>**237 : 8진수로 표현된 int
    - **<u>0x</u>**9f : 16진수로 표현된 int
  - 접미사를 이용해서 정수형 리터럴의 자료형을 지정할 수 있음
    - 123 : int 자료형
    - 123**<u>u</u>** : unsigned int 자료형
    - 123**<u>L</u>** : long 자료형
    - 123**<u>ul</u>** : unsigned long 자료형
    - 123**<u>ll</u>** : long long 자료형

- __문자 리터럴 표현__
  - 작은 따옴표(' ') 안에 문자를 표기하거나, 8진수나 16진수 문자 코드로 표기
    - 'A' : 단순 문자 A를 표현한 것이며, ASCII 코드에 해당되는 정수 65와 동일
    - '\101' : 65의 8진수 표현으로 문자 A를 표현
    - '\x41' : 65의 16진수 표현으로 문자 A를 표현

- __실수 리터럴의 표현__
  - 소수점이나 10의 거듭제곱 표현을 위해 지수기호 e를 사용
    - '1200.' : 1200.0, 12e2, 1.2e+3  (double)
    - '1200.0f' : 12e2f (float)

<br>

### 2-3 변수

- 프로그램이 실행되는 동안 기억해야 하는 값들을 저장하는 메모리 공간을 의미함
- 변수는 반드시 이름(식별자)이 지정되야 하며, 사용 전에 미리 선언됨
- 함수 내부에 선언되면 __지역 변수__ , 함수 외부에 선언되면 __전역 변수__
- 변수의 선언은 __자료형 + 변수명__ 의 형식을 따름
- __`const`__ 는 변수를 상수로 선언하는 키워드로 변수의 값을 초기화 이후 수정하지 못하도록 함
- __`constexpr`__ 은 컴파일하는 시점에 값을 평가한다는 의미로, 런타임(실행) 시점에 비해 좀 더 효율적인 동작이 가능하도록 함
  - 함수 앞에 __`constexpr`__ 이 선언된 경우에는 함수 내의 모든 연산에 대해 컴파일 시점에 값을 구하도록 함

```c++
int a;
std:cin >> a;
const int b = 20; //상수
const int C1 = a;//a가 초기화되어있지 않기 때문에 컴파일 시점에 a의 값을 알지 못하므로 오류 발생
constexpr int C2 = a+10; //마찬가지로 a값이 없기 때문에 오류 발생
constexpr int C3 = b+100; //b는 20으로 초기화되어있기 때문에 컴파일 시점에 오류가 발생하지 않음
constexpr int C4 = C1 * 2; //컴파일 시점에 C2의 초기화에 오류가 있기 때문에 C4 초기화도 오류 발생

constexpr int fac(int n){
    return n>1 ? n*f(n-1) :1 ; //컴파일 시점에 해당 부분 연산 실행
}
```

- 변수의 생명주기(lifetime) : 변수의 생성 및 소멸 시점을 의미하는 것
  - 지역변수 혹은 자동변수의 경우 함수의 시작 및 종료가 각각 생성 및 소멸 시점이 됨
  - 정적 변수(static)의 경우에는 프로그램의 시작 및 종료가 각각 생성 및 소멸 시점이 됨

<br>

### 2-4. 연산자

- 산술연산자 : + , - , * , / , % 
- 대입 연산자 : =
- 복합 대입 연산자 : 산술 연산자와 대입 연산자가 합쳐진 것
- 관계 연산자 : > , < . >= , <=, == , !=
- 논리 연산자 : || , && , !
- __비트 연산자__ : 비트 단위로 논리연산 혹은 왼쪽이나 오른쪽으로 전체 비트를 이동시킬 때 사용

| 비트 연산자 |                             설명                             |
| :---------: | :----------------------------------------------------------: |
|      ~      |   비트를 1이면 0으로, 0이면 1로 반전시킴. (비트 NOT 연산)    |
|      &      |    대응되는 비트가 모두 1이면 1을 반환함. (비트 AND 연산)    |
|     \|      | 대응되는 비트 중에서 하나라도 1이면 1을 반환함. (비트 OR 연산) |
|      ^      |   대응되는 비트가 서로 다르면 1을 반환함. (비트 XOR 연산)    |
|     <<      | 지정한 수만큼 비트들을 전부 왼쪽으로 이동시킴. (left shift 연산) |
|     >>      | 지정한 수만큼 비트들을 전부 오른쪽으로 이동시킴. (right shift 연산) |

<br>

### 2-5. 자료형의 변환

- __묵시적 형변환__

  - 우선순위가 낮은 자료형의 값이 높은 자료형의 값과 동일하게 자동적으로 변환되는 것으로 __연산자 단위로 이루어짐__
  - 대입연산자( __`=`__ ) 를 사용할 경우 저장할 변수의 자료형으로 묵시적 형변환이 이루어짐
    - 실수 -> 정수로의 변환에서 오는 오차 발생이나 데이터의 오버플로우가 발생할 수 있음으로 주의

  ```c++
  intVar = doubleVar + intVar * floatVar;
  
  /*
  	1. intVar*floatVar : float으로 자동으로 형변환
  	2. doubleVar + (float) : double로 자동으로 형변환
  	3. 연산 결과로 얻은 double은 변수 타입에 따라 int로 형변환
  */
  ```

- __명시적 형변환__

  - __`static_cast`__ : 실행 중에 형 검사를 하지 않으며 컴파일 할 때 수식에 지정된 그대로 변환
  - __`dynamic_cast`__ : 기초 클래스나 파생 클래스 간의 포인터 또는 참조 형변환이 프로그램의 실행 중에 일어나도록 지시
  - __`reinterpret_cast`__ : 포인터를 자른 자료형의 포인터나 정수 자료형으로, 또는 역으로 변환
  - __`const_cast`__ : const 지정을 일시적으로 해제

  ```c++
  static_cast<int>(n/10.0);
  
  /*
  static_cast : 형변환 연산자
  <int> : 변환하고자 하는 목표 자료형
  (n/10.0) : 형 변환 대상 수식
  */
  ```

<br>

## 3. C++ 언어 기초(3)

### 3-1. 흐름제어 구문

- __조건문__ : __`if`__ , __`switch`__ 와 같이 조건에 따라 실행의 흐름을 제어하는 문장
- __반복문__ : __`for`__ , __`while`__ , __`do while`__ 과 같이 정해진 범위의 문장을 반복적으로 실행하도록 하는 문장
  - __`for`__ 의 경우 __for( 초기화문장 ; 반복조건 ; 증감문장 )__ 의 형식으로 사용
  - 혹은 __for(개별 원소 : 데이터 집합)__ 의 for each 구문으로도 사용 가능

<br>

### 3-2. 구조체와 클래스

- __구조체__
  - 여러 가지 자료형의 데이터 집합을 저장할 수 있는 새로운 자료형을 정의

```c++
//구조체 사용 예시
struct Coordinates{
    double x,y; //2차원 좌표 x와 y 변수를 담고 있는 구조체
};

struct Circle{
    Coordinates center; //중심 좌표를 나타낼 구조체 변수
    double radius; //반지름을 나타낼 변수
}

//Circle 구조체를 파라미터로 받아 면적을 구하는 함수
double getCircleAreaValue(Circle c){
    
    return c.radius * c.radius * PI;
}
```

<br>

- __클래스__
  - 단순히 인스턴스만을 나열한 것이 아닌, 표현 대상이 어떤 행위를 할 수 있는 지와 어떤 데이터를 저장하는 지(속성)를 하나의 큰 단위로 묶어서 선언한 것
  - 행위는 메소드로, 속성은 가지고 있는 데이터로 나타낼 수 있음
  
- 클래스 사용 예시
  - Circle.h : 멤버 변수와 함수를 선언
  
  ```c++
  struct Coordinates{
      double x,y; //2차원 좌표 x와 y 변수를 담고 있는 구조체
  };
  
  class Circle{
      //속성(멤버 변수)
      Coordinates center; //중심 좌표를 나타낼 구조체 변수
      double radius; //반지름을 나타낼 변수
      
      //행위(멤버 함수)
  	public:
     	 	void init(double x, double y, double r);
      	double area() const;
          bool checkOverLap(const Circle& c) const;
  		void display() const;
  };
  ```
  
  - Circle.cpp : 선언된 함수의 내용을 정해줌
  
  ```c++
  #include "Circle.h"
  #include <cmath>
  #include <iostream>
  using namespace std;
  
  void Circle::init(double x, double y, double r)
  {
      center.x = x;
      center.y = y;
      radius = r;
  }
  
  double Circle::area() const
  {
      return radius * radius * 3.14;
  }
  
  bool Circle::checkOverLap(const Circle& c) const
  {
      double dx = center.x - c.center.x;
      double dy = center.y - c.center.y;
      double dCntr = sqrt(dx*dx + dy*dy);
      return dCntr < radius + c.radius;
  }     
  
  void Circle::display() const{
      cout << "반지름 : " << radius
           << " / 중심 : (" << center.x << "," << center.y << ") /"
           << " 넓이 : " << this->area() << endl;
  }        
  ```
  
  - main.cpp
  
  ```c++
  #include <iostream>
  #include <stdio.h>
  #include "Circle.h"
  
  using namespace std;
  
  
  int main(){
      cout << "원의 면적 구하기" << endl;
      Circle c1, c2;
      c1.init(0,0,10);
      c2.init(30,10,5);
      
      c1.display();
      c2.display();
      
      if(c1.checkOverLap(c2)){
          cout << "두 원은 서로 중첩입니다." << endl;
      }else{
          cout << "두 원은 서로 중첩이 아닙니다." << endl;
      }
      
      return 0;
  }
  
  /*
  출력결과 :
  -------------------------------------------
  원의 면적 구하기
  반지름 : 10 / 중심 : (0,0) / 넓이 : 314
  반지름 : 5 / 중심 : (30,10) / 넓이 : 78.5
  두 원은 서로 중첩이 아닙니다.
  -------------------------------------------
  */
  ```

<br>

### 3-3. 배열

- 동일한 자료형의 값을 여러 개 저장할 수 있는 연속적으로 할당된 공간을 하나의 변수로 묶어 할당한 것
- 배열의 index는 다른 프로그래밍 언어와 마찬가지로 0부터 시작함

```c++
//1차원 배열 선언
float floatArr[4]; //크기 4인 배열 선언
for(int i=0;i<4;i++){
    floatArr[i] = i;
}
```

- 배열 선언시 크기를 나타내는 __`[]`__ 이 늘어날수록 다차원 배열을 표현

<br>

### 3-4. 포인터와 동적 메모리의 할당

- __포인터__

  - 변수, 구조체, 객체 등 값이 저장된 메모리상의 위치를 가리키는 변수

  - 포인터의 선언은 __`*`__ 을 붙여서 나타냄

    ```c++
    int *iPtr; //int형 변수의 메모리 위치를 가리키는 포인터 변수
    ```

- 포인터 활용 예시

  ```c++
  #include <iostream>
  using namespace std;
  
  int main()
  {
      int a = 10; //int 변수 선언
      int *ptr; //int형 포인터 변수의 선언
      
      ptr = &a; //포인터 변수 ptr에 변수 a의 메모리상의 주소를 할당
      cout << "포인터 값 : " << *ptr << endl; // 포인터가 가리키는 변수 a의 값인 10을 출력하게 됨
      
      *ptr = 20; // 포인터가 가리키는 변수 a에 값 20을 할당
      cout << "변수 a의 값 : " << a << endl; //20을 출력하게 됨
      return 0;
  }
  ```

- __동적 메모리 할당__

  - 프로그램의 동작 중에 메모리 공간의 필요성 및 소요량 등을 결정하여 필요한 공간을 할당하는 것
  - 포인터 변수가 할당된 메모리 공간을 가리키도록 함
  - 메모리 공간의 할당은 __`new`__ 키워드의 선언 시점에 이루어지며, 이후 __`delete`__ 키워드의 실행 시점에 소멸하게 됨
  - 정확히는 new의 선언과 함께 __Heap 영역의 일정한 메모리 공간을__ 할당하게 되고, 할당된 메모리 공간의 주소값을 __Stack 영역에 위치한 포인터 변수에__ 할당시키는 것

  ```c++
  #include <iostream>
  using namespace std;
  
  int main()
  {
      int *intPtr;
      intPtr = new int; //intPtr 포인터 변수에 4바이트만큼 heap 공간 할당
      *intPtr = 10; //할당된 공간에 10이란 int 값을 할당
      
      //값 출력해서 확인해보기
      cout << "메모리 주소 : " << (void*)intPtr << endl; //메모리 주소값 출력
      cout << "할당된 값 : " << *intPtr << endl; //할당된 값 10 출력
     	delete intPtr; //메모리 공간 반납 
  }
  ```

  <br>

### 3-5. 참조(Reference)

- 어떤 대상을 가리키는 값을 말하며 포인터 개념과 유사함

- __`&`__ 를 사용하여 참조 변수를 표현

  ```c++
  int a = 10, b = 20;
  int &aRef = a; //참조변수 aRef는 a 변수를 가리킴
  aRef = 100; //aRef가 참조하는 변수 a에 100을 할당
  aRef = b; //aRef가 변수 b를 참조하는 것이 아니라, b에 할당된 값 20을 변수 a에 할당
  ```

- __`const`__ 선언을 통해, 참조 대상의 값을 바꿀 수 없도록 할 수 있음

  ```c++
  int x {10}; //변수 x에 값 10 할당
  const int &xRef = x; //참조변수 xRef가 참조하는 변수 x의 값ㅇ르 바꿀 수 없음
  xRef = 20; //오류 발생
  ```

- 참조변수와 포인터의 차이
  - 일반 변수의 사용과 동일하게 값을 읽거나 저장할 수 있음
  - 참조변수는 반드시 최초 선언시 초기화를 통해 어떤 대상을 참조해야 함
  - 초기화를 통해 지정된 참조 대상을 바꿀 수 없으며, 참조 변수가 유효한 동안에는 하나의 대상만을 참조할 수 있음

<br>

## 4. 함수(Function)

### 4-1. 함수의 정의와 호출

- 함수 : 반복적인 프로그램 문장을 하나의 단위로 모아놓은 후 이름을 부여한 것

  - 매개변수(파라미터)를 통해 함수에서 처리할 데이터를 외부로부터 전달받음
  - 함수는 명시된 처리를 수행한 후, 결과 값을 반환(return)함
  - 함수의 실행이 완료되면, 함수를 호출한 지점으로 다시 복귀하게 됨

- C++에서의 함수 정의 형식

  ```c++
  ReturnType functionName(parameters){
      Type localVarName; //지역변수 선언
      
      // ...
      statement1; //처리할 작업 명시
      
      return returnValue; //처리 결과 반환
  }
  ```

- __Return__ : 함수를 마침과 동시에, 함수가 호출된 곳으로 복귀하라는 명령어임

  - 함수의 어느 위치에서나 사용할 수 있으며, 만일 특정 반환값이 없는 함수의 경우에는 Return Type이 __`void`__ 가 되는 것
  - main 함수의 경우 return이 함수 내에 명시되지 않았을 경우에는, 함수의 맨 끝 도달과 함께 __return 0;__ 이 실행된 것으로 간주

- 함수의 호출 형식

  ```c++
  //함수만 호출
  functionName(parameters);
  //함수의 리턴값을 특정 변수에 할당
  varName = functionName(parameters);
  ```

- 함수 사용 예시

  ```c++
  #include <iostream>
  using namespace std;
  
  float FahrToc(float fahr){
      return (Fahr - 32) * 5 / 9;
  }
  
  int main(){
      float fTemp, cTemp;
      
      cout << "화씨온도 : ";
      cin >> fTemp;
      cTemp = FahrToc(fTemp); //함수 호출해서 cTemp 변수에 값 할당
      cout << "--> 섭씨온도 : " << cTemp << endl;
      retur 0;
  }
  ```

- 이런 식으로 함수를 사용하면, 여러 반복되는 작업을 __`모듈화`__ 함으로써 코드의 불필요한 중복을 방지할 수 있게 됨
- 하지만 함수의 호출과 복귀는 별도의 처리시간을 요하기 때문에, 만일 매우 효율적으로 동작해야 하는 상황이라면 __`inline 함수`__ 를 사용해야 함

<br>

### 4-2. 인수의 전달

- __인수__ : 함수 호출시 함수 내에 전달하는 값으로 매개변수를 통해 전달됨

  - __실 매개변수(actual parameter)__ : 실제 함수 호출시 데이터 처리를 위해 직접 매개변수로 전달한 것
  - __형식 매개변수(formal parameter)__ : 함수 선언시 매개변수 자리에 형식적으로 선언된 것

- __Call By Value__ 방식

  - 실 매개변수의 값을 그대로 형식 매개변수에 복사한 것을 말함
  - 복사되었다는 것은 두 값이 별개의 데이터라는 것을 의미하기 때문에, 자칫 매개변수로 전달된 데이터의 변형으로 인한 부작용이 발생하지 않음
  - 하지만 많은 양의 데이터를 인수로 전달하게 된다면, 그만큼 복사량이 많아지게 됨

- __Call By Reference__ 방식

  - 참조 호출방식이라고도 하며, 실 매개변수의 참조를 그대로 형식 매개변수로 전달하는 것
  - 참조 대상을 이동시키는 것과 같기 때문에, 참조호출에서 형식 매개변수의 값이 변경되는 것은 실 매개변수의 값을 변경하는 것과 같음
  - 형식 매개변수로 복사되는 데이터의 양은, 실 매개변수의 크기와 관계없이 일정함

  ```c++
  #include <iostream>
  using namespace std;
  void SwapValues(int &x, int &y); //함수 원형
  
  int main(){
      int a, b;
     	cin >> a >> b; //a, b를 입력받음
      
      if(a<b){
          SwapValues(a,b); //a와 b의 값을 바꿔줌
      }
      cout << "큰 수 : " << a << ", 작은 수 :" << b << endl;
       return 0;
  }
  
  //참조 호출을 통해 실제 x, y의 값을 변경
  void SwapValues(int &x, int &y){
      int temp = x;
      x = y;
      y = temp;
  }
  ```

- 참조 호출을 통한 효율적인 인수의 전달

  - 많은 양의 데이터를 포함하는 구조체나 객체를 인수로 전달할 경우에는, 참조 호출이 값 호출에 비해 효율적임
  - 이 경우 실 매개변수의 값 변경을 원하지 않을 경우에는, 형식 매개변수의 해당 부분에 __`const`__ 키워드를 선언하여 값을 보호하면 됨

  ```c++
  struct SaleRec {
      //해당 구조체가 포함하는 데이터의 크기만 해도 대략 64바이트 정도임
      char pID[10];
      int dYear, dMonth, dDate;
      char deliverAddr[40];
  }
  
  //참조호출을 하게 되면 4바이트만 복사하면 됨(값 호출은 64바이트 그대로 복사)
  void PrSalesRec(const SalesRec &srec){ 
  	
      /*
  		함수 처리내용 명시
          (하지만 const가 선언되었기 때문에 실제 값의 변경은 불가능)
      */
  }
  ```

  

- __`Default Parameter (디폴트 인수)`__

  - 디폴트값을 인수에 지정할 수도 있음
  - 디폴트 인수는 인수의 맨 끝에만 위치할 수 있음(아닐 경우 오류 발생)

  ```c++
  double Round(double x, int d=0); //파라미터에 d가 없으면 자동으로 d=0
  ```

<br>

### 4-3. 함수의 다중정의(Overloading)

- 함수의 오버로딩 : 동일한 이름을 가진 여러 개의 함수가 정의되는 것을 의미

- 함수의 다중 정의는 __인수(파라미터)의 개수와 자료형__ 의 차이로 구분

  - __리턴값의 자료형으로는 구분할 수 없음__

  ```c++
  //아래와 같은 경우는 오버로딩이 될 수 없음(모호한 상황)
  
  void func(int a){
      //함수 내용
  }
  
  void func(int a, int b=100){
      //함수 내용
  }
  
  int main(){
      /*
      	파라미터의 수는 다르지만, 자료형에 차이가 있기 때문에 모호한 것
      */
      func(10);
      func(10,20);
  }
  ```

<br>

### 4-4. Inline 함수

- 인라인 함수 : __일반적인 함수의 호출 절차를 따르지 않고, 함수 호출 위치에 함수 처리 문장이 삽입되게 번역하도록 선언된 함수__

- 함수 사용을 통한 모듈화의 장점을 살리면서, 함수 호출에 따른 부수적인 처리 시간이 생략된다는 이점이 있음

  - 따라서 빈번히 호출되면서 빠른 실행이 요구될 경우에 인라인 함수를 선언하면 성능 개선에 도움이 됨

- __단, 인라인 함수로 선언한다고 해서 무조건 인라인 함수로 번역되지는 않음__

  - 함수가 너무 큰 경우
  - 재귀 호출(Recursive Call)을 하는 경우
  - 프로그램 내에서 해당 함수에 대한 포인터를 사용하는 경우

- 인라인 함수 예시

  ```c++
  //인라인 함수 선언(두 수를 서로 바꾸는 로직을 수행)
  inline void inlineFunc(int &x, int &y){
      int temp = x;
     	x = y;
      y = temp;
  }
  
  int main(){
      int a,b;
      cin >> a >> b;
      if(a<b) inlineFunc(a,b); //a<b일 경우 두 수를 서로 바꿔줌
      return 0;
  }
  ```

<br>

## 5. 클래스와 객체(1)

### 5-1. 객체지향 프로그래밍의 개념

- __객체(Object)__ : 소프트웨어에서 현실세계에 존재하는 대상들을 표현한 것
  - 정해진 처리를 수행하게 됨(행위, 메소드, 멤버함수 등)
  - 처리 과정에 따라 내부의 상태를 변화시킴(속성, 데이터 멤버)
  - 다른 객체와 상호작용함(멤버함수 호출을 통한 메시지 전달)
- __클래스(class)__ : 객체의 설계도로, 객체가 포함하게 될 속성과 메소드에 대한 정의를 포함함
- __캡슐화(encapsulation)__ : 객체 내부의 상세 구현을 외부 사용자의 관점으로 부터 분리시킨 것
  - 내부 구현에 대한 정보를 은닉하며 객체 외부에서는 공개된 인터페이스를 통해 객체를 사용하게 됨
  - 따라서 캡슐화된 정보에 외부에서의 직접적인 접근이 불가능하기 때문에, 객체 내부의 유지보수가 용이해짐
  - 캡슐화된 객체는 다른 곳에도 응용할 수 있기 때문에 재사용에도 용이
- __상속__ : 자식 클래스는 부모 클래스의 속성을 상속받아, 부모 클래스의 특징을 가지면서 부가적인 속성을 추가해 사용할 수 있음
  - 따라서 여러 클래스가 여러 속성을 공유할 경우, 공통적인 속성들을 부모클래스로 선언한 후 이를 상속받으면 더욱 효율적

<br>

### 5-2. 클래스의 선언과 객체의 정의

-  __클래스__ : 객체의 설계도로 표현하고자 하는 대상의 메소드와 속성을 선언

  - 속성은 객체의 상태를 표현하게 됨
  - 메소드는 객체의 행위를 정의함

- __클래스 선언 형식__

  ```c++
  class ClassName{
      가시성_지시어_1:
      	데이터 멤버 혹은 멤버함수 리스트;
      가시성_지시어_2:
      	데이터 멤버 혹은 멤버함수 리스트;
  }
  ```

- __가시성 지시어__ : 클래스의 멤버가 공개되는 범위를 나타냄

  - __`private`__ : 소속 클래스의 멤버함수와, 형제 클래스의 멤버함수 및 형제함수를 제외한 범위에 모두 비공개
    - 디폴트 값이면서, 정보의 은닉을 위해 사용됨
  - __`public`__ : 모든 범위에 공개
    - 캡슐화 대상이 아닌, 외부에 공개할 인터페이스 선언에 사용됨
  - __`protected`__ : 상속 관계인 클래스에만 공개함

- 일반적인 클래스 선언 예시

  - Counter.h : 클래스의 뼈대가 되는 헤더파일 정의

  ```c++
  class Counter{
      private:
      	int value;
      public:
      	void reset();
      	void count();
      	int getValue();
  };
  ```

  - Counter.cpp : 클래스의 구체적인 멤버와 속성이 정의될 파일

  ```c++
  #include "Counter.h"
  #include <iostream>
  using namespace std;
  
  void Counter::reset(){
      value = 0;
  }
  
  void Counter::count(){
      value++;
  }
  
  int Counter::getValue(){
  
      return value;
  }
  ```

  - main.cpp

  ```c++
  #include <iostream>
  #include "Counter.h"
  using namespace std;
  
  int main()
  {
      Counter counter;
      counter.reset();
      counter.count();
      counter.count();
      
      //Counter 클래스의 value 멤버변수는 private이기 때문에 직접 접근할 수 없음
      cout << "value : " << counter.getValue() << endl;
  
      return 0;
  }
  ```

- 여기서 헤더파일이 중복으로 include된다면 이를 방지해야 함

  - 예를 들어, 위의 예시에서 main.cpp에 Counter.h 헤더파일을 include하는 다른 AnotherCounter.h 헤더 파일을 include하는 경우를 생각해볼 수 있음
  - 이럴 경우 컴파일할 때 선행처리로 Counter.h 에 해당하는 클래스 멤버를 선언한 후, AnotherCounter.h에 해당하는 클래스 멤버를 선언하면서 Counter.h를 또 다시 선언하는 중복 문제가 발생하게 되는 것
  - __`#ifndef`__ , __`#define`__  , __`#endif`__ 패턴을 이용해서 중복 include를 방지할 수 있음
    - __#ifndef__ : 특정 키워드가 정의되지 않았음을 확인
    - __#endif__ : 정의된 경우에는 endif가 있는 블록까지의 코드를 무시하는 것

  ```c++
  #ifndef COUNTER_H_INCLUDED
  #define COUNTER_H_INCLUDED
  class Counter{
      //...
  };
  #endif
  ```

  ```c++
  #ifndef ANOTHERCOUNTER_H_INCLUDED
  #define ANOTHERCOUNTER_H_INCLUDED
  #include "Counter.h"
  class AnotherCounter{
      //...
  };
  #endif
  ```

  - 혹은 __`#pragma once`__ 선언을 통해, 헤더 파일을 한 번만 적용하도록 선언할 수도 있음

  ```c++
  #pragma once
  class Counter{
      //...
  };
  ```

  ```c++
  #pragma once
  #include "Counter.h"
  class AnotherCounter{
      //...
  };
  ```

- __`const`__ 키워드를 선언하면, 클래스 내에서 해당 데이터 멤버의 값은 수정할 수 없음

  ```c++
  class Counter{
      private:
      	const int value = 4; //value=4로 고정
      public:
      	void reset();
      	void count();
      	int getValue();
  };
  ```

  ```c++
  #include <iostream>
  #include "Counter.h"
  using namespace std;
  
  int main()
  {
      Counter counter;
      counter.reset();//value값을 변경하려고 할 경우 오류 발생
  	//...
      return 0;
  }
  ```

<br>

### 5-3. 생성자(Constructor)

- 객체의 생성 시점에 수행할 작업을 정의하는 멤버 함수

- 매개변수를 통한 인수 전달이 가능하며, 여러개의 생성자를 정의할 수 있음

- 클래스의 이름과 동일하며, 리턴값과 리턴 자료형이 존재하지 않음

- 생성자는 public으로 선언해야 외부에서의 객체 생성이 가능해짐

  ```c++
  class ClassName{
      //...
      public:
      	ClassName(parameters){
              //...객체 생성시 수행할 작업 명시
          }
  }
  ```

- 5-2에서 사용한 Counter 클래스에 생성자를 적용해보기

  ```c++
  class Counter{
      int value;
      public:
      	//생성자 선언, 최초 Counter 객체 생성시 value=1로 두고 시작하는 것
      	Counter(){
              value = 1;
          }
      	void reset(){
              value = 0;
          }
      	void count(){
              value++;
          }
      	int getValue(){
              return value;
          }
  }
  ```

- __초기화 리스트__ : 생성자의 머리에 데이터 멤버를 초기화하는 값들을 나열한 것

  - 데이터멤버이름{초기값} 의 형태를 사용

  ```c++
  class Counter{
      int value;
      public:
      	Counter() : value{0} {
              // value =0은 위에서 정의하고, 이후에 수행할 내용을 명시
          }
  }
  ```

<br>

### 5-4. 소멸자(Destructor)

- 객체의 소멸 시점에 수행할 작업을 정의하는 멤버 함수

- 클래스 이름에 __`~`__ 를 붙여 선언하며, 생성자와 마찬가지로 리턴값을 명시하지 않음

- 생성자와 달리 매개변수를 포함하지 않으며, 오버로딩 또한 불가능

- 일반적으로 __`public`__ 으로 선언함

- 만일 상속을 통해 자식 클래스를 정의하게 될 경우에는, __`virtual`__ 을 지정하여 가상함수가 되도록 하는 것이 좋음

- 소멸자 사용 예시

  - Person.h

  ```c++
  #ifndef PERSON_H_INCLUDED
  #DEFINE PERSON_H_INCLUDED
  class Person{
      private:
  	    char *name; //이름
      	char *address; //주소
      public :
      	Person(const char *name, const char *address);
      	~Person();
      void print() const;
      void changeAddress(const char *newAddress);
  };
  #endif 
  ```

  - Person.cpp

  ```c++
  #include <iostream>
  #include <cstring>
  #include "Person.h"
  using namespace std;
  
  Person::Person(const char *name, const char *address){
      this->name = new char[strlen(name)+1];
      strcpy(this->name,name); //메모리 주소값 복사
      this->address = new char[strlen(address)+1];
      strcpy(this->address,address); //메모리 주소값 복사
      cout << "Person 객체 생성(" << name << ")" << endl;
  }
  
  //소멸자 선언
  Person::~Person(){
      cout << "Person 객체 제거(" << name << ")" << endl;
      delete [] name; //메모리 반납
      delete [] address; //메모리 반납
  }
  
  void Person::pring() const{
      cout << address << "에 사는 " << name " 입니다." <<endl;
  }
  
  void Person::changeAddress(const char *newAddress){
      
      delete [] address;
      address = new char[stren(newAddr)+1]; //새로운 메모리 공간 할당
      strcpy(address,newAddress); //address에 새로운 메모리 주소값 복사
  }
  ```

  - Main.cpp

  ```c++
  #include <iostream>
  #include "Person.h"
  using namespace std;
  
  int main(){
      Person *p1 = new Person("김철수","서울특별시 광진구");
      Person *p2 = new Person("김영희","서울특별시 광진구");
      p1 -> print();
      p2 -> pring();
      cout << endl << "주소 변경 : ";
      p2 -> changeAddress("서울특별시 송파구");
      p2 -> print();
      delete p1;
      delete p2;
      return 0;
  }
  ```

<br>

## 6. 클래스와 객체(2)

### 6-1. 디폴트 생성자

### 6-2. 복사 생성자

### 6-3. 이동 생성자

### 6-4. static 키워드



