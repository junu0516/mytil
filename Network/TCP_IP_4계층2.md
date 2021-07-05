# TCP/IP 4계층 2 - IP, TCP&UDP



> 여기서는 4계층에서 간단히 다뤘던 IP와 TCP&UDP 등의 프로토콜에 대해 좀 더 자세히 살펴보도록 한다.
>
> 

![Data-Flow-of-TCP/IP-protocol](https://www.elprocus.com/wp-content/uploads/Data-Flow-of-TCP-IP-protocol.jpg)



### IP(Internet Protocol)와 IP 주소

- 인터넷 계층에서 활용되는 주요 프로토콜로, __`End-to-End`__ 통신을 수행하는 것이 주요 역할임
- 데이터 전송을 위해서는 전달받은 세그먼트에 IP 헤더를 추가하여 __`IP 패킷`__ 만들어야 하는데, 이는 쉽게 말해 네트워크 상에서의 출발지와 목적지 정보를 데이터에 추가하는 것을 의미함.
  - 어플리케이션계층과 트랜스포트 계층을 거치면서 데이터에 http 헤더와 TCP 헤더가 붙어있는 상태에서 IP헤더가 추가적으로 붙는 것
- 출발지에서 내보내진 IP 패킷은 네트워크상의 __`라우터`__ 를 거쳐, 최종 목적지로 도착하게 되며 이를 __`라우팅`__ 이라고 부름
- __`IP 주소`__ 는 통신 상대가 되는 호스트들을 식별하기 위한 정보로, __네트워크 상에서 호스트의 주소 정보__ 정도로 이해하면 됨
  - IP 헤더에 출발지와 목적지의 IP 주소가 지정되어 있으며, TCP/IP 통신이 정상적으로 이루어지기 위해서는 필수적임
  - IP주소는 보통 호스트 내부에서 이더넷 등의 인터페이스와 IP 프로토콜을 연관지어 설정됨.
    - PC에는 복수의 유, 무선의 __복수의 인터페이스__ 가 탑재되어 있으며, 이러한 __인터페이스마다 IP주소를 설정__ 할 수도 있음
    - __IP 주소는 엄밀히 말해 호스트 자체가 아닌, 호스트 내의 인터페이스를 식별하는 것__

- IP주소는 32비트이며, 8비트씩 0~255 사이의 10진수로 변환하여 '.'으로 구분하여 표기함(__`도트형 10진 표기`__)
- 이용 범위에 따라 __`공인 IP주소`__ 와 __`사설 IP주소`__ 로 나눠볼 수 있는데, 공인IP 주소는 전체 인터넷에서 중복되지 않게 관리되며, 사설 IP 주소는 특정 사설 네트워크 내에서 이용되기 때문에 사설 네트워크 내에서만 중복되지 않는다면 문제되지 않음
  - 사설 네트워크에서 통신을 하는 경우에는, __`NAT(Network Address Translation)`__ 을 통해 IP 주소를 변환하며, __`라우터`__ 는 나중에 응답이 돌아올 경우를 대비해서, 변환한 주소의 대응을 따로 __`NAT 테이블`__ 에 보존해뒀다가 응답이 라우터로 오면 목적지 IP 주소를 테이블을 보고 다시 사설 IP 주소로 변환함



### 유니캐스트, 브로드캐스트, 멀티캐스트

> IP 프로토콜로 데이터 전송시, 목적지는 여러개여도 상관이 없으며, 따라서 데이터 전송 방식을 목적지 수에 따라 3가지로 분류할 수 있음

- __유니캐스트__
  - __단 하나의 목적지__ 로 데이터를 전송하는 것을 말하며, 유니케스트에 이용하는 IP 주소가 곧 유니캐스트 IP주소가 됨
    - 대부분의 TCP/IP 통신에서 설정되는 IP 주소는 유니캐스트 IP 주소임
    - __네트워크부 + 호스트부__ 의 구조로 이루어져 있으며, 네트워크부를 이용해 각 네트워크를 식별하고 호스트부를 통해 네트워크 내 호스트의 인터페이스를 식별함
    - 여기서 IP 주소의 네트워크 영역을 따로 나눈 것을 __`서브넷(Subnet)`__ 이라고 하며, IP 주소에서 어디까지가 네트워크부인지 IP 주소 형태로 명시한 것을 __`서브넷마스크(Subnet Mask)`__ 라고 함
  - PC 등에서는 유니캐스트 IP주소를 설정하며, 유니캐스트 데이터 전송 시 목적지 호스트의 유니캐스트 IP 주소를 IP 헤더의 목적지 IP로 지정
  - 완전히 같은 데이터를 복수의 주소로 전송하고자 할 경우에는, 목적지의 수 만큼 유니캐스트 데이터 전송을 반복해야 하므로 효율이 좋지 않음
- __브로드캐스트__
  - __같은 네트워크 상의 모든 호스트__ 에 완전히 똑같은 데이터를 전송하는 것을 말함
  - IP 헤더의 목적지 IP 주소에 브로드캐스트 IP주소를 지정하면, 같은 네트워크 상에서 모든 호스트에 데이터를 전송할 수 있음
  - 도트형 10진 표기로는, __`2555.255.255.255`__ 가 브로드캐스트 IP 주소를 의미하며, 이는 32비트가 모두 1로 이루어진 주소임
    - 호스트부의 비트를 모두 1로 채운 것
- __멀티캐스트__
  - 같은 어플리케이션이 동작하는 등의 __특정 그룹에 포함되는 호스트__ 에, 완전히 똑같은 데이터를 전송하는 것을 말함
  - 멀티캐스트 그룹에 포함되는 호스트라고 해서, 반드시 같은 네트워크에 속한다고 볼 수는 없음
  - IP헤더의 목적지 IP 주소에는 멀티캐스트 IP 주소를 지정함
  - 보통 __224.0.0.0 ~ 239.255.255.255__ 로 미리 범위가 정해져 있으며, 일례로 __`224.0.0.2`__ 와 같은 멀티캐스트 주소는 같은 네트워크 상의 모든 라우터 그룹을 의미함
    - 사용자가 자유롭게 그룹을 결정하기 위해 239로 시작되는 범위를 이용할 수도 있음
    - 호스트부의 비트를 모두 0으로 채워 네트워크부 만을 식별한 것

- 여기서 3가지 방식 모두 출발지 IP는 유니캐스트 IP주소가 됨



### ICMP(Internet Control Message Protocol)

- IP 프로토콜로 네트워크상의 다른 호스트에 데이터를 전송하지만, 사실 IP 프로토콜 자체는 응답이 제대로 돌아오지 않는 경우에 대해서는 딱히 대처하지 않고 있음(Best Effort)
- 별도로 End-to-End 통신이 잘 이루어졌는지 확인하기 위해 __`ICMP`__ 라고 불리는 프로토콜을 활용하는 것
- ICMP의 주요 기능은 에러 리포트와 진단 기능으로 나눠볼 수 있음
- 어떤 오류가 발생하여 IP 패킷이 폐기되었다면, 폐기한 기기는 ICMP를 이용해 폐기된 IP 패킷의 출발지로 통신 실패의 원인이 담긴 에러 리포트를 전송하는데, 이를 __`도달불능 메시지`__ 라고 부름
- 진단 기능은 End-to-End 통신이 가능한 지 여부를 확인하는 것이며, __`ping`__ 명령어를 통해 미리 요청/응답 메시지를 보내 지정된 IP 주소와의 통신이 가능한 지를 확인함



### ARP(Address Resolution Protocol)

- 네트워크 연결 계층을 거치면서(이더넷 인터페이스) IP 패킷에는 이더넷 헤더가 붙는데, 여기에는 목적지의 __`MAC 주소`__ 가 지정됨
- 여기서 목적지의 MAC 주소를 식별하기 위해 __`ARP`__ 가 사용되는 것이며, IP 주소와 MAC 주소를 대응시키는 것을 가리켜 __`주소 해석`__ 이라고 부름
- 주소 해석의 범위는 같은 내트워크 내의 IP 주소이며, 보통 이더넷 인터페이스가 목적지 IP 주소를 지정하면서 자동으로 ARP가 수행됨
  - ARP 요청을 보내 IP 주소에 대응하는 MAC 주소가 무엇인지 물어봄
  - 요청 받은 IP 주소에 해당하는 호스트는, ARP 응답을 통해 MAC 주소를 알려줌
  - IP 주소와 MAC 주소의 대응을 ARP 캐시에 보존시킴



### TCP(Transmission Control Protocol)

![http://www.mdpi.com/2076-3417/6/11/358/htm](http://itwiki.kr/images/thumb/a/a6/3-way-handshake.png/500px-3-way-handshake.png)

- TCP/IP에서 제공되는 신뢰성이 제공되는 연결지향형 데이터 전송 서비스를 일컫음

- __신뢰성 보장, 흐름 제어, 혼잡 제어__ 등의 역할을 수행함

  - 목적지로 전송할 데이터를 전송에 알맞은 __`최대 세그먼트 크기(Maximum Segment Size)`__ 로 절단하여, __`세그먼트(Segment)`__ 단위로 데이터를 전송

    - 따라서 데이터의 경계를 따로 구분하지 않음

  - __`검사합(checksum)`__ 이라고 불리는, 일종의 중복 및 오류 검사를 수행하며, 데이터 전송의 순서가 보장됨

  - 패킷 전송시 TCP 프로토콜은 보통 일정한 시간을 정해놓고, 제 시간 안에 ACK 패킷이 도착하지 않으면 이를 Timeout으로 간주하여 패킷을 재전송하여 신뢰성 보장에 기여함

  - __흐름 제어__ : 수신자 버퍼의 오버플로우를 방지하는 것을 의미

  - __혼잡 제어__ : 전송되는 패킷이 과도하게 많아지는 상황을 방지함을 의미

    

- 사전에  __`3 Way Handshake`__ 를 통해 어플리케이션 간 통신이 정상적으로 이루어질 수 있는 지 확인하며, 이것이 TCP의 연결지향적 특성을 갖게 하는 주요 특징이라 할 수 있음
  - 클라이언트가 서버에 접속을 요청하는 __`SYN 패킷`__ 을 보내며, 응답을 기다리는 __`SYN_SENT`__ 상태가 됨
  - 서버가 포트가 열린 상태(__`Listen`__)에서 패킷을 받으면 __`SYN_RESEIVED`__ 상태가 되며, 데이터를 송신해도 좋다는 의미로 __`ACK 패킷`__ 으로 응답하며, 클라이언트로부터의 응답을 기다림
  - 클라이언트는 응답을 받으면 다시 서버로 __`ACK패킷`__ 보내고, 이후부터는 TCP 커넥션이 이루어진 __`ESTABLISHED`__ 상태가 되어 데이터를 주고받을 수 있게 됨

- 데이터의 온전한 상태 유지 및 전송 순서 보장 등의 이점이 있지만, 앞서 언급한 3 Way Handshake와 같은 연결 설정 등으로 인해 후술할 __`UDP`__ 에 비해 상대적으로 속도가 느림

- 또한 1:1 통신만 가능하다는 한계가 있음

  

  #### >> TCP 헤더

  

  ![TCP 와 UDP [동작원리/헤더/차이점] : 네이버 블로그](data:image/png;base64,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)

  

  - 보통 TCP 세그먼트라고 함은, 어플리케이션 계층에서 보내진 데이터에 TCP 헤더가 추가된 것을 말함

  - TCP 헤더에는 출발지와 목적지 포트번호, 시퀀스 번호, ACK 번호, 검사합 등의 요소가 존재

    - __시퀀스 번호__ : 데이터 전송의 순서 보장을 위한 정보로, 데이터가 분할된 경우에는 시퀀스 번호를 통해 어떻게 분할되었는 지 알 수 있음(전송되는 바이트 수를 기준으로 점점 증가)

      - 앞서 언급한 __`MSS`__ 기준 세그먼트가 분할되는 것과 대응되는 내용!

    - __ACK 번호__ : 응답 번호라고도 하며 수신측 프로세스에서 수신한 바이트 수를 제대로 응답하기 위해 사용됨

    - __Flags__ : SYN, ACK 등의 제어 번호가 담긴 제어 비트 영역

    - __Window Size__ : 수신 윈도우의 버퍼 크기를 지정할 때 사용되는데, 사이즈가 0일 경우에는 송신 프로세스가 전송을 중지함

    - __포트 번호__ : TCP 프로토콜을 통해 연결되는 송, 수신측(출발지-목적지 양 끝)에 할당되는 포트 번호

    - __검사합(checksum)__ : 세그먼트에 포함된 헤더 및 데이터에 대한 오류 검출 용도

      

### UDP(User Datagram Protocol)



![img](https://blog.kakaocdn.net/dn/db4ChG/btqNkfv2W6U/RPlPzrfgr0YbZUa7XLcrw1/img.png)



- __비연결, 비신뢰적 특징__ 을 갖는 전송계층의 프로토콜을 일컫음
- 사전 연결 과정 없이 일단 데이터그램을 전송하고 보기 때문에, 빠른 전송이 가능하지만 신뢰성은 떨어짐
  - 동일하게 데이터그램의 전송 순서 또한 보장되지 않으며 중간에 유실되어도 재전송하지 않음
  - TCP가 MSS 사이즈로 패킷을 나누는 등의 데이터 분할 기능도 존재하지 않음
    - 이는 TCP와 달리 데이터의 경계가 구분됨을 의미 
  - TCP와 달리 1:1, 1:N, N:N 통신이 모두 가능함

- UDP가 제공하는 서비스는 최소한의 신뢰성을 위한 오류제어 메커니즘으로 __검사합(checksum)__ 기능을 옵션으로 제공할 뿐임(TCP와의 공통점)

  - UDP 헤더는 TCP 헤더에 비해 포트번호에 검사합 관련 정보 정도가 추가된 정도로 비교적 간단함

- 결국 신뢰성 보장 등을 위해서는 어플리케이션에서 따로 추가적인 작업이 필요해짐

  

  #### >> UDP 헤더
  - 단순히 출발지와 목적지 포트번호, 데이터그램의 크기, 검사합의 정보만이 포함됨

    
