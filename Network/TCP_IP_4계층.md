# TCP/IP 4계층



> 네트워크 아키텍쳐에는 여러 가지 프로토콜이 있지만 오늘날은 거의 TCP/IP를 사용하고 있음
>
> TCP/IP는 TCP와 IP를 중심으로 한 프로토콜의 집합으로 네트워크의 공통 언어라 할 수 있음



![Chapter 02 TCP/IP Packet Transmission - 1](https://microchip.wdfiles.com/local--files/tcpip:tcp-ip-five-layer-model/tcpip_5_layer_overview.png)



### TCP/IP의 계층구조

- TCP/IP 프로토콜에서 네트워크 통신을 위해, 각종 기능을 계층화하고 복수의 프로토콜을 조합하여 실현
  - OSI 참조 모델을 실제로 이용하기 위해 단순화된 모형이 TCP/IP 4계층이라 할 수 있음
- __네트워크 연결 계층, 인터넷 계층, 트랜스포트 계층, 어플리케이션 계층__ 의 총 4계층으로 구성됨 

- 이러한 계층간 구분을 통해 각 기능들의 __서로 간에 간섭을 최소화__ 할 수 있으므로, 유지와 보수에 있어 편리함
  - 특정 계층에서 문제가 생기면, 그 계층을 살펴보면 되기 때문
- 서로 다른 계층들 끼리는 데이터의 전달 과정을 구체적으로 알 필요가 없기 때문에, __데이터의 캡슐화__ 와 은닉이 가능함
  - 헤더의 첨부를 통해 캡슐화가 이루어짐  

  

### 제 4계층 : 어플리케이션 계층(Application Layer)

- 사용자와 가장 가까운 계층으로 사용자-소프트웨어 간 소통을 담당하는 계층
- 어플리케이션의 기능을 실행하기 위한 데이터의 형식 및 처리절차 등을 결정하며, 데이터는 인간이 인식할 수 있는 문자나 이미지와 같은 형식으로 표현됨
- 다양한 프로토콜이 사용되며 대표적으로 80번 포트를 사용하는 __http 프로토콜__ 이 있음  

  

### 제 3계층 : 트랜스포트 계층(Transport Layer)

- 데이터의 전송 및 흐름에 있어 __신뢰성 보장__ 을 담당하며, 이는 다시말해 데이터를 적절한 어플리케이션에 제대로 전달되도록 배분함을 의미
  - __End-to-End의 신뢰성을 확보__
- 어플리케이션 계층에서 트랜스포트 계층까지 바르게 동작하면, 출발지와 목적지 어플리케이션 간에 제대로 된 데이터의 송수신이 가능해짐

- 대표적인 프로토콜로 __`TCP`__ 와 __`UDP`__ 가 있음
  - __TCP__ : 연결지향형 프로토콜로, 패킷에 하나의 오류라도 있으면 재전송을 통해 에러를 복구함
    - 헤더에 붙는 정보가 많지만 그만큼 신뢰성 있는 데이터의 전송이 보장됨
  - __UDP__ : 패킷을 중간에 잃거나 오류가 발생해도 이에 대처하지 않고 계속해서 데이터를 전송
    - TCP에 비해 간단하나, 신뢰성이 보장되지 않기 때문에 데이터 전송의 효율성이 신뢰성보다 중요한 경우에 적합한 방식임

  

### 제 2계층 : 인터넷 계층(Internet Layer)

- 네트워크 상에서 데이터의 전송(패킷의 이동)을 담당하며, 쉽게 말해 서로 다른 네트워크 간의 통신을 가능하게 하는 역할을 수행하는 것
- 데이터의 이동경로를 다루는데, 여기서 출발지와 목적지의 표시를 위한 논리적인 주소로 __`IP주소`__ 를 할당
  -  IP주소는 네트워크 상에서 컴퓨터를 식별하는 번호로 기능하며, 보통은 32비트의 숫자롤 8비트씩 끊어서 표현한 __`IPv4`__ 가 보편적이지만, 오늘날 이것이 포화상태이기 때문에 범위를 확장시켜 128비트의 주소 체계를 활용한 __`IPv6`__ 가 사용되고 있음
- 네트워크끼리 연결하고 데이터를 전송하는 기기를 __`라우터`__ 라고 하며, 라우터에 의한 네트워크 간 전송을 __`라우팅`__ 이라고 함
  - 라우터는 내부의 __`라우팅 테이블(Routing Table)`__ 을 통해 경로 정보를 등록하여 데이터 전송을 위한 최적의 경로를 찾음
  - 이렇게 출발지와 목적지 간의 데이터 전송 과정을 가리켜 __`End-to-End 통신`__ 이라고 부름
  - 쉽게 말해, 출발지에서 목적지까지 여러 라우팅 과정을 거쳐 최종적으로 End-to-End 통신이 수행되는 셈



### 제 1계층 : 네트워크 연결 계층(Network Access Layer)

- 물리적인 데이터의 전송을 담당하는 계층으로, 여기서는 인터넷 계층과 달리 같은 네트워크 안에서 데이터가 전송됨

- 제 1계층을 __`물리적 계층(Physical Layer)`__ 과 __`데이터 링크 계층(Data Link Layer)`__ 로 나눠서 TCP/IP 계층구조를 5개로 나누기도 하는데, 여기서 물리적 계층은 하드웨어적 측면의 기능을 담당하며, 네트워크 간의 데이터의 전송은 데이터 링크 계층이 담당함

  #### >> __데이터 링크 계층(Data Link Layer)__

  - 네트워크 장비 간에 데이터를 전송하는 역할을 수행하며, __`이더넷(Ethernet)`__ 프로토콜을 사용함

  - 데이터 전송 과정에서의 오류를 감지하는 역할도 하며, 이는 __`이더넷 헤더`__ 와 함께 인터넷 계층에서 전달받은 __`패킷`__ 의 반대쪽 끝에 __`트레일러(trailer)`__ 을 붙임으로써 수행됨

  - 이더넷은 LAN과 WAN에서 사용되는 기술규격으로 MAC 패킷과 프로토콜의 형식을 정의함

    > __LAN(Local Area Network) & WAN(Wide Area Network)__
    >
    > LAN은 근거리 통신망을 의미하며 특정 거점 내의 PC나 서버의 통신을 가능하게 하며, 복수의 거점에서 통신을 하기 위해 LAN끼리 연결한 것을 WAN이라고 함

  - 실제로 데이터가 전송될 목적지를 식별하기 위해 __`MAC 주소`__ 를 사용하며, 이는 논리적 주소인 IP와 달리 제조사에 의해 할당된 고유 주소로 컴퓨터의 물리적인 주소를 나타냄

  #### >> 물리 계층(Physical Layer)

  - 0과 1로 표현된 이진 데이터를 전기 신호로 변환하거나, 전기 신호를 이진 데이터로 변환하는 역할 수행
  - 여기서 전기 신호의 전달은 유, 무선의 전송매체를 통해 이루어짐
  - 대표적으로 __`허브(hub)`__ 라는 장비를 사용하는데, 이는 전기 신호를 전형하고 증폭하는 기능을 함
    - 허브에는 여러 개의 포트가 있어, 여러 컴퓨터와의 연결이 가능함
    - 특정 포트에 연결된 컴퓨터로부터 데이터를 받았을 때, 여기서 어떤 포트로 전송할 지 결정하지 않고 일단 나머지 모든 포트로 받은 데이터를 전송함
    - 최종 목적지 컴퓨터로의 데이터 전송 판단은 이후 데이터 링크 계층에서 헤더에 나타난 MAC 주소를 보고 최종 판별하는 것

  

### 데이터 전송의 흐름

![img](https://blog.kakaocdn.net/dn/drvQM4/btqSdt4T2TY/xckBtJ9gBIlIknEI7lAymK/img.png)

- TCP/IP의 각 계층을 거치면서 데이터에는 여러 프로토콜의 헤더가 추가되며, 각 계층에서 데이터를 부르는 방법은 서로 구분됨
  - 여기서 각 계층별로 헤더를 추가하여 정보를 감싸는 것을 __`캡슐화(Encapsulation)`__ 이라고 함
- 데이터를 역으로 수신할 경우에는 1계층에서 4계층으로 가면서 해당 계층별로 필요한 헤더를 확인 후 헤더를 삭제함
- 1계층에서 4계층까지 순서대로 __`프레임(frame)`__ , __`패킷(packet) 혹은 데이터그램(datagram)`__ , __`세그먼트(segment) 혹은 데이터그램`__ , __`메시지(message)`__ 로 데이터가 구분됨
  1. 전형적인 웹브라우저에서의 통신의 경우, 우선 데이터에 __`http 헤더`__ 가 추가되면서 http 메시지가 됨
  2. 트랜스포트 계층을 거치면서 http 메시지에 __`TCP 헤더`__ 가 추가되면서 세그먼트가 됨
     - 만일 UDP 프로토콜을 사용할 경우에는 세그먼트 대신 데이터그램이 됨
  3. 인터넷 계층을 거치면서 IP 주소 정보가 포함된 __`IP 헤더`__ 가 추가되어 패킷이 됨
  4. 패킷에 __`이더넷 헤더`__ 와 FCS가 추가되어 프레임이 됨

