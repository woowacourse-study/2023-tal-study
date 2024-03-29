CHAPER 2 질문

1. 2단원-2-(b) : 프로토콜 스택이 애플리케이션의 데이터를 저장 송신하는 기준이 내부 송신용 버퍼(소켓 버퍼의 데이터를 조각으로 나누어 네트워크로 전송하 ㄹ떄 사용. 조각된 패킷이 내부 송신용 버퍼에 사용**되는 것)**

   **프로토콜 스택이 애플리케이션의 데이터를 저장하는 기준이라는 게 뭐야?? 어디에 저장한다는 거임?**

   **데이터를 어떤 시점에 네트워크 송신을 위한 내부 송신용 버퍼에 복사하는지에 대한 기준**

    1. **데이터 양과 크기:** 프로토콜 스택은 애플리케이션이 생성하는 데이터 양과 크기에 따라 얼마나 많은 데이터를 한 번에 내부 송신용 버퍼에 저장할지 결정할 수 있습니다.
    2. **네트워크 속도와 효율성:** 네트워크 속도와 혼잡도 등을 고려하여 어느 정도의 데이터를 한 번에 송신하는 것이 효율적인지 판단합니다.
    3. **지연 시간과 신뢰성:** 데이터를 빠르게 송신하기 위해 애플리케이션 버퍼에 일정량의 데이터가 모일 때까지 기다리지 않을 수 있습니다. 그러나 이에 따른 데이터의 지연과 손실 가능성을 고려해야 합니다.
    4. **ACK 수신 및 피드백:** 네트워크로부터 수신되는 ACK 패킷 등을 기반으로 데이터의 송신 시점을 조절할 수 있습니다.

   프로토콜 스택은 이러한 기준을 고려하여 애플리케이션 버퍼의 데이터를 네트워크로 전송하기 위한 내부 송신용 버퍼에 저장하는 시점을 결정합니다. 이렇게 함으로써 네트워크의 효율성과 성능을 최적화하며 데이터 전송을 관리합니다.

2. 2단원-5-3 : 패킷을 송신할 때 어떤 LAN 어댑터에서 보내야 하는지 결정하는 과정

   해당 네트워크와 연결된 인터페이스를 결정한다. (인터페이스 = 랜어댑터) 네트워크로 보내기 위해 이 어댑터를 사용해야 함


---
# 2단원.  TCP/IP의 데이터를 전기 신호로 만들어 보낸다

## (1) 소켓을 작성한다

1. 프로토콜 스택 내부구성
    - 프로토콜 스택이란 OS에 내장된 네트워크 제어용 소프트웨어
    - 애플리케이션이 소켓 라이브러리를 통해 데이터 송수신을 의뢰하면(요청 버퍼링 - socket - connect - write(복사 및 알림) - 프로토콜 스택이 읽어서 패킷 변환
    - 프로토콜 스택 아래 = IP, ICMP, ARP
    - Internet Control Message Protocol(ICMP) : IP 프로토콜 패밀리로, 네트워크 **장치간의 통신 및 네트워크 문제 진단을 위한 프로토콜**로, IP 패킷 이용해 제어 및 오류 메시지를 전송한다
        - 네트워크 상의 문제 발생 시, ICMP는 **오류메시지 생성해 송신자에게 문제의 원인과 상황을 알린다**
        - 핑과 트레이스 루팅과 같은 도구의 **기반을 제공**합니다. 핑은 목적지 호스트까지의 응답 시간을 측정하고 네트워크 상태를 확인하는 데 사용되며, 트레이스 루팅은 IP 패킷의 경로를 추적하여 네트워크 경로를 확인하는 데 사용
        - **네트워크 장애 및 상태 모니터링**: ICMP는 **네트워크 장애나 상태를 모니터링**하기 위해 사용될 수 있습니다. 예를 들어, 호스트가 접속 가능한지 확인하거나 라우터와의 연결 상태를 확인하는 용도
    - Address Resolution Protocol : 네트워크 장치가 IP 주소를 MAC 주소로 매핑하거나 반대로 MAC 주소를 IP 주소로 매핑하는 데 사용되는 프로토콜로 로컬 네트워크에서 동작한다.
        - 호스트나 라우터가 로컬 네트워크 내에서 통신할 때, 해당 목적지의 IP 주소에 대응하는 MAC 주소를 알아내야 한다. ARP는 이러한 IP 주소를 MAC 주소로 매핑하는 역할한다
2. 소켓의 실체는 통신 제어 정보
    - 소켓은 내부에 통신 동작 제어하기 위한 메모리 영역
        - 제어 정보: 통신 상대의 ip, 포트번호, 통신 동작이 어떤 상태에 있는가 기록
        - 소켓은 개념적인 것이고, 제어 정보를 기록한 메모리 영역을 소켓이라고 한다.
    - 프로토콜 스택은 소켓이라는 제어정보를 참조해 동작한다
        - 예_ 응답 돌아오지 않으면 다시 보내야하는데, 소켓에 응답 돌아옴 여부와 송신 이후 경과시간이 기록됨
    - 소켓의 내용 :  프로토콜 종류 / 로컬의 ip와 포트번호 /통신 상대측의 ip와 포트번호 / 통신상태(Listening(상대 접속을 기다리는 상태), Established(접속 동작이 끝나고 데이터가 통신 중)) / pid( Process ID :소켓을 사용하는 프로그램의 식별번호)
    - 소켓의 생성이란?
        - 제어정보를 추가하고, 송 수신 데이터를 일시적으로 저장하는 버퍼메모리를 준비하는 등 통신을 준비하는 작업]

    1. 소켓 호출 시 동작
        1. **소켓 생성 : 소켓 1개부의 메모리 영역 확보 및 초기 상태를 나타내는 제어 정보를 기록**
        2. 디스크립터를 애플리케이션에 알림 (소켓 생성 완료)

       이후에는, 소켓 연결 connect할 때나(ip, 포트번호) - write로 데이터 송수신 의뢰할 때(ip,포트번호, 소켓버퍼, 통신상태), 디스크립터로 소켓 찾아서 제어정보 활용한다.

       3. 소켓 생성의 의미
              소켓은 네트워크 통신의 중간 단계에서 사용되는 인터페이스로서, **프로토콜 스택과 애플리케이션 간의 상호작용을 관리하고 제어하는 역할**을 수행


## (2) 서버에 접속한다

클라이언트측의 소켓을 서버측의 소켓에 접속하는 단계.

1. 접속의 의미
    1. **서버에 접속한다 = 서로 제어정보를 주고받는다**(서버에 로컬의 Ip,포트번호,pid 넘겨줌 + 통신 상태 등) ⇒ 데이터 송수신 가능한 상태로 만듦(+ 소켓 버퍼 메모리 확보)
    2. 소켓을 처음 만들었을 때는 아무것도 기록되지 않으므로, 통신 상대가 누구인지도 모름. connect함수 호출해 통신 상대 정보를 소켓에 저장하고 연결을 설정한다. = **서버의 ip주소나 포트번호 정보를 소켓에 저장해 프로토콜 스택에게 알리는 것도 접속이다.**
2. 헤더의 형태로 **제어 정보를 주고 받는다**
    - 데이터를 저장한 패킷과, 제어정보만 있는 패킷
        - 제어 정보만 있는 패킷 = 제어 동작이나 연결 끊기 동작 등 애플리케이션 데이터가 없는 경우
    - 애플리케이션에서 통지된 정보, 통신 상대로부터 받은 정보, 송수신 진행 상황 등의 제어정보가 소켓에 기록
    - 소켓에 기록한 제어 정보는 상대측에서 볼 수 없음
    - 즉, 통신 동작에 기록되는 제어정보는 헤더에 기입되는 정보(통신용)/ 소켓(프로토콜 스택의 메모리영역)에 기록되는 정보

1. 접속 동작의 실체

   = connect 호출하는 것

    - connect 호출시, 디스크립터와 서버측의 ip 주소, 포트번호 (소켓유형-TCP인지, 프로토콜, 타임아웃 -통신작업의 제한시간, 소켓옵션)등 소켓 연결에 필요한 정보 → 프로토콜 스택의 TCP 담당부분에 전달.
    - 클라이언트의 TCP 담당부분이 제어 정보 기록한 TCP헤더 만들고 ip에 송신 의뢰
    - 서버에 패킷 동작하면, 서버측의 TCP 담당 부분이, **헤더에 기록된 수신처 포트번호에 해당하는 소켓을 찾아낸다**
    - 서버의 해당 소켓이 발견되면, **이 소켓에 필요한 정보를 기록하고, 접속 동작이 진행중인 상태가 된다**
    - 접속 성공 시, **클라이언트의 소켓에 서버의 ip주소나 포트번호 등과 함께 소켓에 접속 완료를 나타내는 제어 정보를 기록**


## (3) 데이터를 송 수신한다

프로토콜 스택은 애플리케이션에서 의뢰받은 메시지를 작은 조각으로 분할해 서버를 향해 송신

1. 프로토콜 스택에 HTTP 리퀘스트 메시지를 넘긴다

   순서

    1. 애플리케이션(브라우저)이 **직렬화된 데이터 준비 및** write 함수 호출
    2. 송신데이터를 프로토콜 스택에 넘겨준다
    3. 일단 스택 내부의 송신용 버퍼 메모리 영역에 저장하고, 애플리케이션이 다음 데이터를 주기를 기다림

       이유 : 애플리케이션에서 프로토콜 스택에 넘기는 데이터 길이는 애플리케이션 종류, 방법에 따라 다름

       = 한 번의 송신 의뢰에서 건네 주는 데이터 길이는 애플리케이션 사정에 따라 결정됨

    4. **어느정도 데이터를 저장하고 나서 송 수신 동작**을 한다. (패킷으로 변환한다든지 등)

   송수신 동작 시작 기준

    1) 한 패킷에 저장할 수 있는 데이터의 크기

    - MTU(**패킷 한 개로 운반할 수 있는 디지털 데이터의 최대 길이**)라는 매개변수로 받아 판단한다
    - MSS : 헤더를 제외하고 한 개의 패킷으로 운반할 수 있는 TCP의 데이터의 최대 길이

   = 데이터가 mss에 가깝게 이를 때까지 저장하고, 송신동작을 실행한다

    2) 타이밍

    - 애플리케이션의 송신 속도가 느려지는 경우, 최대로 데이터를 저장하려면 시간이 오래 걸린다. -> 송신 동작 지연
    - 따라서 **프로토콜 스택은 내부에 타이머를 두고, 일정 시간 이상 경과하면 데이터가 최대로 모이지 않아도 패킷을 송신**한다.
    - 두 판단 기준은 서로 다른 점을 중시한다.
        - `네트워크 이용 효율(MTU) <-> 송신 동작 지연 방지(타임아웃)`
    - **어떻게 판단할지는 프로토콜 스택을 만드는 개발자에게 맡겨져 있으므로**, OS 종류나 버전에 따라 동작이 달라진다.
    - 또, 애플리케이션 측에서도 송신의 타이밍을 제어하는 여지도 있다
        - 데이터 **송신을 의뢰할 때 '버퍼에 머물지 않고 바로 송신할 것'으로 옵션을 지정**할 수 있다.

2.  데이터가 클 때는 분할하여 보낸다

    예) 블로그에 글을 업로드할 때 등

    송신 버퍼에 저장되는 데이터는 mss 길이를 초과하므로 다음 데이터를 기다릴 필요가 없다.

    송신 버퍼에 들어있는 데이터를 mss 크기에 맞게 분할 → 분할 조각을 패킷에 넣어 송신

3. ACK 번호를 사용하여 패킷이 도착했는지 확인한다

   TCP에는 송신한 패킷이 상대에게 올바르게 도착했는지 확인하고, 도착하지 않았으면 다시 송신하는 기능이 있다.

    - 시퀀스 번호와 ACK 번호로 패킷의 도착을 확인한다.
        - 시퀀스 번호: **데이터를 분할할 때, 분할한 각 조각이 통신 개시부터 따져서 몇 번째 바이트에 해당하는지 세어둔 값**을 TCP 헤더에 기록한 것

          수신 측은 **이 시퀀스 번호를 통해 세그먼트의 순서를 파악**하고 데이터를 올바른 순서대로 재조립. 시퀀스 번호는 **세그먼트의 데이터 바이트 위치**를 나타내며, **수신 측이 어떤 데이터를 기대하는지를 알려주는 역할 = 송신 데이터의 순서를 식별**

        - ACK 번호: **수신 측이 성공적으로 받은 데이터의 마지막 바이트 (의 위치)**.

          송신 측이 전송한 세그먼트 중에 수신 측이 **제대로 받은 세그먼트의 마지막 바이트를 ACK 번호로 표시하여 송신 측에게 어떤 데이터까지 성공적으로 도달했는지를 알려줌**. 이를 통해 **송신 측은 수신 측의 상태를 파악하고, 필요한 경우 재전송이나 흐름 제어 등을 수행**할 수 있습니다.

            - 패킷의 누락이 없는 것을 확인할 수 있다.
            - **수신 측에서** ACK 번호를 **되돌려주는 동작**으로 'xx번째 바이트까지 수신했다'고 응답하는 것을 **수신 확인 응답**이라고 부른다.
        
       실제로는 시퀀스 번호가 1부터 시작하지 않고, 난수를 바탕으로 산출한 초기값으로 시작한다. 이 때 **초기값을 통지하는 SYN의 제어 비트를 1**로 하여 보낸다.

   ### 동작 순서

    - 접속 동작
        1. 클라이언트에서 서버에  연결 요청 위해 SYN 패킷 보냄. 이 패킷에는 초기 시퀀스번호 포함.
        2. 서버는 클라이언트의 시퀀스번호 확인하고, 이를 기반으로 ACK번호 설정(초기 시퀀스 번호+1)
        3. 서버는 SYN-ACK 패킷을 생성하여 클라이언트에게 통지(이 패킷에는 서버의 초기 시퀀스번호와 클라이언트로 부터 받은 ACK번호 포함)
    - 송수신 동작
        1. 클라이언트는 **`SYN-ACK`** 패킷을 받고, 내부의 서버의 시퀀스 번호(데이터 순서)를 확인하고 ACK 번호(수신 성공한 데이터 순서)를 설정한다. = 서버의 초기 시퀀스 번호에 1을 더하여 ACK 번호
           이제 클라이언트와 서버는 초기화된 시퀀스 번호와 ACK 번호를 모두 알고 있습니다. (둘다 동일한 상태)
        2. 데이터를 포함한 TCP 세그먼트를 생성하고, 서버에게 보내기 위해 시퀀스 번호를 설정

           = 이전에 사용한 시퀀스 번호 기반으로 데이터 크기만큼 시퀀스 번호 증가시켜 설정(데이터 순서 식별)

        3. 서버는 클라이언트로부터 받은 데이터를 처리하고, **서버가 받은 시퀀스 번호에 1을 더한 값을 ACK 번호로 설정**하여 클라이언트에게 ACK 패킷을 보냅니다.

4. 패킷 평균 왕복 시간으로 ACK 번호의 대기 시간을 조정한다

   ack번호가 돌아오는 것을 기다리는 대기시간 = 타임아웃값

   ack번호가 안돌아오면 다시 보내기를 하는데, 소모적인 다시보내기를 방지하기 위해 이 대기시간을 적절히 길게 설정해야 한다.

   이 타임아웃값은 동적으로 변경한다.

    - 데이터 송신(보내기) 시, 항상 ack번호가 돌아오는 시간을 계측
    - **ack번호가 돌아오는 시간이 지연/ 짧아짐에 따라 대기 시간도 그만큼 동적으로 바꾼다.**

5.  윈도우 제어 방식으로 효율적으로 ACK 번호를 관리한다

    ack 번호 기다리는 동안 아무것도 안하는 것은 시간낭비임.

    ### 윈도우 제어방식이란?

    **: 한개의 패킷을 보낸 후 ack번호를 기다리지 않고 차례대로 연속해서 복수의 패킷을 보내는 방법**

    목적 = 데이터의 신속한 전송을 위해 도입된 기능(ack번호가 돌아올 때까지의 시간이 낭비되지 않음)

    주의할 점: 수신측의 수용 능력을 초과하여 패킷을 보내는 사태

    1. 수신측에서 송신측에 수신 가능한 데이터 양을 통지하고
    2. 수신측의 수신용 버퍼 메모리에 데이터를 임시 보관
    3. 수신 버퍼에 빈 부분이 생기면, 그 분량만큼 수신할 수 있는 양이 늘어남 ⇒ **tcp헤더의 윈도우 필드에 빈 영역을 알림 (윈도우 사이즈 = 수신 가능한 데이터 양의 최대값)**



6. ACK 번호와 윈도우를 합승한다

   송수신 동작의 효율성 높이기 위해 윈도우 값 통지 타이밍

    - 윈도우 통지 동작은 언제 일어날까?

      수신 데이터가 수신 버퍼에 보관되면, 윈도우를 하나하나 차례대로 수신측에 통지할 필요 없음

      이유 : 윈도우 값은 송신측이 송신할 때마다 감산해서 스스로 산출 가능하기 때문이다.

      **⇒ 수신측이 수신 버퍼에서 데이터를 추출해, 애플리케이션에 건네 줄 때이다.**

      수신측의 애플리케이션이 언제 데이터 read요청할 지 송신측은 알 수 없으므로, 데이터 건네주고 버퍼의 빈 영역이 늘어났을 때, 이것을 송신측에 통지한다.

    - ACK번호는 언제 통지하나?
        
        수신측에서 데이터를 받았을 때, 정상 수신인 경우에만. 즉, 데이터 수신한 후 즉시 보낸다.
        
    - 문제점 : ACK번호 통지하고, 잠시후 버퍼 비었을 때, 윈도우를 송신측에 통지하는 상태 = 송신측에 보내는 패킷이 많아져 효율성 저하
    
    - 해결방법 : ack번호나 윈도우 통지시, 바로 보내지 않고 잠시 기다리고, 다음 통지 동작이 일어나면 양쪽을 한 개의 패킷으로 묶어서 보낸다.
        
        경우의 수1 ) ack번호 통지가 연속해 일어나면, 최후의 것만 통지 (윈도우 통지도 마찬가지)


7. HTTP 응답 메시지를 수신한다

   http 리퀘스트 메시지 받으면, 웹 서버에서 응답 메시지 돌아오기를 기다리고, 돌아오면 수신

   이때의 프로토콜 스택의 동작

    1. read프로그램을 호출
    2. 바로 응답메시지 돌아오지 않으니, 작업을 잠시 보류하고 있다가, 응답 패킷이 도착하면 작업 재개
    3. 프로토콜 스택이 움직여, 데이터 일시 보관하는 수신 버퍼를 사용하는데
    4. 수신 버퍼에서 데이터 추출하여 애플리케이션 건네준다.
        - 수신한 데이터 조각과 tcp헤더 내용 조사해 데이터 누락을 검사하고
        - 문제 없으면 ack번호를 반송
        - 데이터 조각을 수신 버퍼에 일시 보관하고
        - 조각을 연결해 데이터를 원래 모습으로 복원 후
        - 애플리케이션이 지정한 메모리 영역에 옮겨 기록한 후, 애플리케이션에게 제어를 되돌려준다
        - 데이터 건네주고, 타이밍 가늠하여 윈도우를 송신측에 통지(ack번호와 합승할 수도 있음)


## (4) 서버에서 연결을 끊어 소켓을 말소한다

1. 데이터 보내기 완료했을 때 연결을 끊는다

   애플리케이션이 송신해야 하는 데이터를 모두 송신 완료했다고 판단했을 때

   송신을 완료한 측이, 연결 끊기 단계. 어디서 데이터 송수신 끝나는지는 애플리케이션에 따라 다름

   어느쪽에서 먼저 연결 끊기 해도 괜찮다

   예) 웹 브라우저, 서버에서 응답 메시지 받으면 데이터 보네ㅐ기 완료 = 서버측이 연결 끊기 시작

   서버측에서 연결 끊기

    - 소켓 라이브러리의 close호출
    - 서버의 프로토콜스택이 tcp 헤더 만들고, 연결 끊기 나타내는 장보 FIN플래그에 1 설정
    - 클라이언트에 송신
    - 서버측의 소켓에 연결 끊기 동작 진행중이라는 정보 기록

   클라이언트 측,

    - 서버에서 FIN 1인 패킷 도착하면, 자신의 소켓에 서버측 연결끊기 동작 했다는 것 기록
    - 서버측에 ACK번호 반송
    - 애플리케이션이 또 read를 호출하여 데이터 가지러 올 때까지 기다림
    - 이번엔 데이터 건네지 않고, 서버에서 보낸 데이터 전부 수신완료했다는 사실을 알림
    - 서버에서 보낸 데이터 전부 수신 완료하면 클라이언트도 종료
    - close 호출하고,
    - 서버에 FIN 송신 및 ACK돌아오면 연결 종료

2. 소켓을 말소한다

   서버와 연결 종료하면, 바로 소켓을 말소하지 않고 잠시 기다린다

   예) 클라이언트가 연결끊기 시작해서 소켓 close하는데,
   서버입장에서 클라이언트의 ACK번호가 돌아오지 않으면, 다시한번 FIN을 보낼 수 있음. 이때 클라이언트 소켓이 말소되어 있다면?

3. 정리


## (5) IP와 이더넷의 패킷 송 수신 동작

## 1. 패킷의 기본

헤더에는 적절한 제어 정보 기록하고(소켓에 있는 제어정보 중) - 데이터 함께 가까운 중계장치에 송신

중계장치는 도착한 패킷의 헤더를 조사해, 패킷의 목적지를 판단. 표에 수신처의 주소는 xxx케이블 이라고 써있으면, xxx케이블에 송신. 다음 중계장치에 도착.

최종적으로 패킷이 수신처의 기기에 도착하는 것

### 라우터와 허브

서브넷에서 **라우터와 허브라는 두 종류의 중계장치는 다음과 같은 열할을 분담하며 패킷을 운반한다.**

- 라우터 = 목적지를 확인하여 다음 라우터를 나타냄

  = ip규칙에 따라 패킷을 운반 = ip 목적지를 확인해, **다음 ip의 중계장치를 조사한다**

- 허브 = 서브넷 안에서 패킷을 운반해 다음 라우터에 도착

이더넷 규칙에 따라 패킷을 운반 = **서브넷 안에 있는 이더넷이 중계장치까지 패킷을 운반**한다.

### 패킷 운반 과정


### 데이터 패킷이 웹브라우저에서 서버로 전송되는 과정.

1. 데이터 분할과 프로토콜 스택: 소켓 버퍼에 저장된 데이터는 프로토콜 스택에 의해 세그먼트로 분할됩니다. 이 세그먼트에는 **TCP 헤더와 데이터가 포함되어 네트워크 전송을 위해 준비**됩니다.
2. 패킷화 : 패킷에는 목적지 주소와 다른 네트워크 정보가 포함됨




3. mac 헤더 :  **ARP (Address Resolution Protocol)를 사용해,**  호스트가 IP 주소를 가지고 있을 때, 해당 IP 주소에 대한 MAC 주소를 알수 있음

4. 패킷은 허브에 도착하는데 : nic(네트워크 인터페이스 카드)→ 네트워크 케이블 통해 허브로 전달
5. 허브의 역할 : 허브는 이더넷 표 == MAC 주소 테이블 가짐. 이더넷 표는 **네트워크에 연결된 장치들의 MAC 주소를 기록**하는데, 이를 사용해 허브는 **수신된 패킷을 어느 포트로 전달할지 결정**
6. 라우터의 역할: 허브를 통과한 패킷은, 라우터R1에 도착. **라우터는 IP용 표를 사용하여 패킷의 다음 경유지를 결정**(패킷의 목적지가 되는 **서버의 IP가 네트워크에 있는지를 조사**하고, 그 방향에 있는 다음 라우터를 조사)


7. 패킷의 전송: **라우터는 패킷의 다음 라우터로 중계하기 위해 다음 라우터의 MAC주소를 조사**,MAC 헤더에 기록하여 다음 라우터에 송신.
8. 다음 라우터의 도착: 패킷이 다음 라우터에 도착하면, 이 라우터는 IP 헤더의 수신처 정보와 IP 용 표를 조합하여 패킷의 다음 경유지를 결정합니다. 이후 패킷에 MAC 헤더를 추가하고 다음 라우터로 패킷을 중계합니다.
9. 목적지에 도착: 패킷이 중계되는 과정을 거쳐 목적지에 도착하게 됩니다. 목적지에서 패킷을 수신하고 처리하는 동작이 완료됩니다.

## 2. 프로토콜 스택의 ip담당 부분의 패킷 송신동작 알아보기

- ip담당부분은 패킷을 상대에게 송출하기만 한다. 패킷을 운반하는 것은 허브나 라우터 같은 네트워크 기기의 역할
- ip 담당부분에 패킷 송신을 의뢰 → IP 담당부분은 세그먼트 앞에 IP헤더와 MAC헤더 부가.
    - ip 헤더 = ip주소로 목적지까지 패킷 전달에 사용하는 제어 정보 기록한 것
        - mac 헤더 = 이더넷 등의 랜을 사용해 가장 가까운 라우터까지 패킷 운반할 때 사용하는 제어정보 기록한 것 (이더넷용 헤더)
- 이렇게 만든 패킷을 네트워크용 하드웨어에 건네준다

  이더넷이나 무선 랜 등(랜 어댑터) = 이진 디지털 데이터가 랜 어댑터에 의해 전기나 빛의 신호 상태로 바뀌어 케이블로 송출 → 허브나 라우터 등 중계장치에 도착


## 3. 수신처 ip주소를 기록한 ip헤더를 만든다

### 수신처 IP 주소

- **IP는 스스로 수신처를 판단하지 않고**, 애플리케이션이 지정한 상대에게 송신한다.
- 즉, 잘못된 상황이 발생하면 **애플리케이션측의 책임**으로 간주한다.

### 송신처 IP 주소

> 하나의 컴퓨터에 여러개의 LAN 어댑터가 장착되어 있다면, IP는 몇 개일까?
>
- **IP 주소는 컴퓨터에 할당되는 것이 아니라, LAN 어댑터에 할당**된다. = 주로 사설 주소
- 여러 개의 LAN 어댑터를 장착하면 각각 서로 다른 IP 주소가 할당되는 것이다.
- 이 경우, 컴퓨터에할당된IP 주소가여러개가되므로 **어느 IP 주소를 설정해야할지 판단해야 한다**
    - **어느 LAN 어댑터를 사용해서 패킷을 송신할지 판단하는 것과 같음**
    - LAN 어댑터가 결정되면 IP 주소가 결정되기 때문

> 어떤 LAN 어댑터에서 패킷을 송신해야 하는지 확인한 후,
**LAN 어댑터에 할당 되어 있는 IP 주소를 IP 헤더의 송신처 IP 주소로 설정**한다.
>

### 라우팅 테이블 살펴보기(라우터의 IP용 표를 이용해 다음 라우터를 결정)

1. 수신처 IP의 NetworkIP부분과 라우팅 테이블의 `Network Destination` 항목 비교해서 일치하는 것 찾아낸다
2. 인터페이스 항목 =  LAN 어댑터 등의 네트워크용 인터페이스

   ip주소 할당한 인터페이스를 통해 왼쪽의 라우터로 패킷을 보낸다

3. `Gateway` = 중계 대상 라우터의 주소 = **다음 라우터의 IP주소가 기록되어 있음**

   = 이 라우터가 목적지에 패킷을 중계해준다는 뜻

    - 라우팅 테이블 맨 위 행에는 목적지와 `넷 마스크`가 `0.0.0.0` 으로 등록되어 있고, 기본 게이트웨이를 나타낸다.
    - 만약 라우터를 나타내는 'Gateway 항목과 Interface' 항목의 IP주소가 같으면 라우터로 중계하지 않고 상대에게 직접 패킷을 전할 수 있는 것. 이 경우에는 소켓의 수신처 IP 주소에 직접 패킷을 건네준다

### 패킷을 송신할 때 어떤 LAN 어댑터에서 보내야 하는지 결정하는 과정

1. **수신처 IP의 Network IP와 라우팅 테이블의 Network Destination 비교하기**

   : 패킷의 목적지 IP 주소의 네트워크 부분과 라우팅 테이블의 Network Destination과 **일치하는 항목을 찾습니다. 이것은 목적지 IP가 어떤 네트워크에 속하는지 확인하는 단계**입니다.

2. **인터페이스 결정**

   : 목적지 IP 주소가 속한 네트워크를 찾으면 **해당 네트워크와 연결된 인터페이스를 결정한다**.

   (인터페이스 = 랜어댑터) 해당 네트워크로 패킷 보내기 위해 어떤 어댑터를 사용해야 할지 결정할 수 있다.

3. **게이트웨이 설정**: **해당 어댑터와 연결된 라우터의 IP 주소인 게이트웨이를 확인**합니다. 이 라우터는 목적지까지 패킷을 중계해주는 역할을 합니다. **라우팅 테이블에 있는 게이트웨이 정보를 기반으로 패킷을 다음 라우터로 전송**합니다.
    - 만약 라우팅 테이블에 등록된 게이트웨이와 인터페이스의 IP 주소가 동일하다면, 이는 다음 라우터가 아니라 직접적인 연결로 패킷을 보낼 수 있는 것입니다.

### 프로토콜 번호

> 패킷의 내용물을 어디서 의뢰 받은 건지 표기
>
- TCP: 06 (16진수 표기)
- UDP: 17 (16진수 표기)
- 값은 규칙에 결정 되어 있음

## 4. 이더넷용 MAC 헤더를 만든다.

> IP 헤더를 만든 후에는 MAC 헤더를 만든다.
>
>
> 이더넷에는 TCP/IP 개념이 통용되지 않는다.
>
> **이더넷의 수신처 판단 구조로 사용하는 것이 MAC 헤더** 이다.
>

### MAC 헤더

> MAC 헤더에는 수신처와 송신처의 MAC 주소, 이더 타입(=사용하는 프로토콜 종류) 정보가 있다.
>
- IP 주소는 32비트이지만, MAC 주소는 48비트 이다.
- IP 주소는 `동, 번지` 와 같이 그룹화 개념이지만, MAC 주소는 한 개의 값으로 생각해야 한다.
- 이더 타입: 내용물이 무엇인지 나타냄.
    - 내용물은 IP나 ARP라는 프로토콜 소켓이며, 각각 대응하는 값이 규칙으로 정해져 있어서 여기에 값을 기록한다.

### MAC 헤더 만들기

- 이더 타입 필드에는 프로토콜을 나타내는 값 설정
    - IP 프로토콜: 0800
    - ARP 프로토콜: 0806
- 송신처 MAC 주소
    - **자체 LAN 어댑터의 MAC 주소**
    - **MAC 주소는 제조할 때 그 안에 있는 ROM에 기록**. 이 값을 읽어와서 설정
    - LAN 어댑터가 여러 개일 경우 LAN 어댑터를 먼저 선택하고 설정
- 수신처 MAC 주소
    - 라우팅 테이블을 통해 패킷을 줄 상대를 조사한다.
    - 라우팅 테이블에서 **gateway의 IP 주소의 기기가 패킷을 넘겨줄 상대**가 된다. (IP계층에서 알려줌)
    - 상대의 MAC 주소를 조사해서 헤더에 기록하면 된다.

## 5. ARP로 수신처 라우터의 MAC 주소를 조사한다.

> 상대의 MAC 주소를 조사할 때 쓰이는 것이 ARP이다.
>
>
> 이더넷에는 연결되어 있는 전체에게 패킷을 전달하는 브로드캐스트라는 구조가 있다.
>
> 이를 이용해 상대가 같은 네트워크에 존재한다면 MAC 주소를 알 수 있다.
>

### ARP 캐시

- 한 번 조사한 결과는 ARP 캐시라는 메모리 영역에 보존하여 다시 이용한다.
- 우선적으로 ARP 캐시 조사하고, 없으면 ARP 조회를 진행한다.
- ARP 캐시를 이용해 ARP의 패킷은 줄일 수 있으나, 그 값을 계속 사용하면 문제가 생길 수 있다.
    - ex. IP 주소를 설정해서 고친 경우
- ARP 캐시에 저장된 값은 시간이 지나면 삭제된다 (OS에 따라 다름)

## 6. 이더넷의 기본

> 이더넷은 다수의 컴퓨터가 여러 상대와 자유롭게 적은 비용으로 통신하기 위해 고안된 통신 기술이다.
>
>
> 이더넷도 IP와 마찬가지로 패킷의 내용물은 보지 않는다.
>

### 이더넷의 원형 (10BASE5)

> 케이블을 통해 신호가 전체에 흐름
>
- 네트워크의 실체는 케이블만 있다.

### 리피터 허브를 이용한 파생형 (10BASE-T)

> 신호가 리피터 허브에서 흩뿌려져서 전체에 전달됨
>
- 트렁크 케이블이 리피터 허브로, 트랜시버 케이블이 트위스트 페어 케이블로 바뀜
- 여전히 신호가 전원에게 전달됨

### 스위칭 허브를 이용한 형태

> 스위칭 허브는 수신처 MAC 주소에 따라 목적지를 확인하고 패킷을 중계하기 때문에 신호는 원하는 상대에게만 흐름
>
- 스위칭 허브를 사용
- 현재의 이더넷의 형태
- 전원에게 신호가 전달되지 않고, 수신처 MAC 주소로 나타내는 기기가 존재하는 부분에만 신호가 흐름

## 7. IP 패킷을 전기나 빛의 신호로 변환하여 송신한다.

> IP가 만든 패킷은 메모리에 기억된 디지털 데이터 이다.
>
>
> 디지털 데이터를 네트워크 케이블에 송출하기 위해서는 전기나 빛의 신호로 변환해야 한다.
>

### LAN 어댑터


- 변환 작업을 실행하는 것은 LAN 어댑터이다.
- LAN 어댑터를 제어하기 위해 **LAN 드라이버 소프트웨어**가 필요하다(하드웨어의 공통적인 사항).
- LAN 드라이버는 LAN 어댑터 제조 업체 전용 제품을 사용해야 한다.
- LAN 어댑터는 전원을 공급하면 초기화 작업 후 사용할 수 있다.
    - LAN 드라이버가 하드웨어 초기화 작업을 수행해야 가능
    - 하드웨어 이상 검사, 초기 설정 등의 작업이 수행된다(하드웨어 공통).
    - **MAC 회로에 MAC 주소를 설정한다(이더넷 특유 작업).**

      이때의 맥 주소는 제조할 때 기록한 mac주소를 가져와 mac 회로에 설정한다.


### MAC 주소 설정

- LAN 어댑터의 ROM에 MAC 주소를 제조할 때 기록하므로, 이 것을 읽어와 설정한다.
    - 이 때 MAC 주소는 중복되지 않도록 일원화 해서 관리한다.
    - **이렇게 할당된 MAC 주소를 자체적으로 알수도 있지만, 명령이나 설정파일에서 받아 설정하는 경우도 있다. 이 때, LAN 어댑터의 ROM에 기록한 것은 무시**한다.
        
      = 

      일반적으로 네트워크 인터페이스의 고유한 MAC 주소가 제조사에 의해 ROM(Read-Only Memory)에 미리 기록되어 있는 경우에도, 사용자나 관리자가 직접 설정파일이나 명령어를 통해 MAC 주소를 변경할 수 있는 상황을 나타냅니다.

      즉, ROM에 기록된 MAC 주소는 기본값이며, 사용자나 관리자가 필요에 따라 이를 변경할 수 있습니다. 일반적으로는 MAC 주소는 고유성을 유지하기 위해 제조사에 의해 할당되며 변경되지 않습니다. 그러나 일부 상황에서는 네트워크 관리 목적이나 보안 이유로 MAC 주소를 수동으로 변경하는 경우가 있을 수 있습니다


## 8. 패킷에 3개의 제어용 데이터를 추가한다.

> LAN 드라이버는 IP 담당 부분에서 패킷을 받으면 LAN 어댑터의 버퍼 메모리에 복사한다.
>
>
> 복사를 마친 후에는 **MAC 회로에 명령을 보내고, MAC 회로의 작업이 시작된다**.
>


### 제어용 데이터

1. MAC 회로는 송신 패킷을 버퍼 메모리에서 추출한다.
- 패킷의 앞쪽(왼쪽)에 `프리앰블` 과 `스타트 프레임 딜리미터` 데이터를 부가하고, 뒤쪽(오른쪽)에는 `프레임 체크 시퀀스(FCS)`를 부가한다.
    - FCS: 오류 검출용 데이터
    - 프리앰블
        - **송신하는 패킷을 읽을 때 타이밍을 잡기 위해 사용**
        - 1과 0이 번갈아 나타나는 비트열이 56비트 이어진 것.
        - 1010 비트 패턴을 신호로 바꾸면 일정한 형태의 파형이 나타남
        - 수신측은 신호를 수신할 때 이 파형에서 타이밍을 판단하며, 1비트 분 신호의 중앙 위치를 판단한다.
- 스타트 프레임 딜리미터
    - 끝이 11이라는 비트 패턴
    - 이로 인해 파형이 변함
    - 패킷의 개시 위치로 간주한다.

### 클록

> 디지털 데이터를 전기 신호로 나타낼 때는 **0과 1의 비트 값을 전압이나 전류의 값에 대응**시킨다.
>
>
> 각 비트의 구분이 어디까지인지 판단하면서 전압 또는 전류의 값을 읽어야 함 그런데 만약 1과 0이 이어지면 **신호 변화가 없어지는데 비트를 구분할 수 있을까?**
>


- 이 때 사용할 수 있는 쉬운 방법은 **데이터를 나타내는 신호와 별도로 비트 구분을 나타내는 클록 신호를 보내는 것이다**.
    - 클록 신호는 **주기적으로 반복되는 신호로, 시간을 나타내는 역할**을 합니다. 이 클록 신호의 각 주기에 따라서 데이터 신호의 상태를 읽어내면, 각 비트의 값을 알아낼 수 있습니다. 예를 들어, 클록 신호의 상승(edge)가 발생할 때마다 데이터 신호를 읽어보면, 해당 시점에서 데이터가 0인지 1인지를 판별할 수 있습니다.
    - **각 클록 신호의 상승(edge) 시점에서 데이터를 읽어보면**, 해당 시점에서 데이터 비트의 값이 무엇인지를 알 수 있습니다. 이를 통해 클록 신호의 주기에 따라 데이터의 비트 값을 정확하게 알아낼 수 있습니다.
- 클록 신호가 **아래에서 위로 변화할 때** 데이터 신호의 전압과 전류를 읽고 0과 1로 대응  시킨다.
- 클록 신호는 일정주기로 결정된 모습으로 변화하는 신호. (b)
- 따라서 변화의 타이밍을 알고 있으면. 수신한 신호 c에서 클록신호를 추출하고, 원래 데이터 신호 a를 추출할 수 있다.

  = 클록 신호로 타이밍을 잡아 데이터 신호에서 비트값을 읽을 수 있다.

- 하지만 거리가 멀어져서 케이블이 길어지면 신호선의 길이가 달라져서 **데이터 신호와 클록 신호가 전달되는 시간 차**가 발생되는 문제
- 클록 신호의 타이밍을 판단하는 것이 중요함. 클록이 변화하는 주기는 결정되어 있으므로, 잠시 신호의 변화를 볼 수 있으면 타이밍을 파악할 수 있음(에스컬레이터와 같이) = 갑자기 패킷의 신호를 흘리는 것이 아니라, 클록 신호의 타이밍을 잡기위한 특별한 신호를 패킷앞에 부가


-

### 클록 신호 + 데이터 신호

> 데이터 신호와 클록 신호를 합성하여 한 개의 신호로 만들어 위 문제를 해결 가능
>
- 클록 신호와 데이터 신호를 합성한 신호에서 전압을 결정하는 방법은 특정 표에 따른다 (교재표 참고).
- 송신 측에서 수신측으로 전압을 결정하는 특정 표를 보낸다.

### 프리앰블의 역할

- 클록 신호의 타이밍을 판단하는 것은 중요하다.
- 클록 신호의 타이밍을 잡기 위한 특별한 신호를 패킷 앞에 부가하는데, 이 것이 프리앰블의 역할이다.

### 스타트 프레임 딜리미터

- 스타트 프레임 딜리미터는 패킷의 시작을 나타내며, 수신측은 이를 통해 데이터를 추출한다.

### FCS

> 패킷을 운반하는 도중에 잡음 등의 영향으로 파형이 흐트러져 데이터가 변한 경우에 검출하기 위해 사용
>
- 32비트의 비트열
- 패킷의 맨 앞부분 ~ 맨 끝까지의 내용을 특정 계산식에 기초하여 계산한 것
- CRC라는 디스크 장치 등에 사용하는 오류 검사 코드와 같은 종류
- 패킷 운반 도중 데이터가 변하면 수신측에서 계산한 FCS가 송신할 때 계산한 것과 다른 값이 된다.
- 이 불일치를 통해 데이터 변질 사실을 검출함

## 9. 허브를 향해 패킷을 송신한다.

> 리피터 허브를 사용할 때는 반이중 모드, 스위칭 허브를 사용할 때는 전이중 모드를 사용한다.
>

### 반이중 모드

> 신호의 충돌을 피하기 위한 동작들을 살펴보자
>
1. 케이블에 다른 기기가 송신한 신호가 흐르는지 조사
2. 신호가 흐르고 있다면 기다림(충돌 방지)
3. 신호가 정지하거나 원래 흐르지 않고 있었다면 송신 시작
- 송신 동작
    - MAC 회로가 프리앰블(타이밍 신호)의 맨 앞부터 1비트씩 디지털 데이터를 전기 신호로 변환
    - 변환된 신호를 **송수신 신호 부분(PHY나 MAU)**에 보낸다.
        - 이더넷 신호 방식에 따라 Medium Attachment Unit 또는 100메가비트/초 이사의 고속 이더넷을 PHY라고 부른다.
    - 디지털 데이터를 신호로 변환하는 속도 = 전송 속도
    - PHY(MAU) 회로는 신호를 케이블에 송출하는 형식으로 변환하여 송신함

- 이더넷은 케이블의 종류, 전송 속도에 따라 다양한 신호 형식이 규정되어 있음
    1. MAC 회로는 형식의 차이에서 벗어나 어느 형식으로도 변환할 수 있는 공통 형식의 신호를 PHY(MAU)회로에 보낸다

    1. PHY(MAU) 회로에서 실제로 신호를 케이블에 송출하는 형식으로 변환 후 송신함

       = PHY(MAU) 회로 : **MAC 회로가 송신한 신호의 형식을 변환하는 변환 회로**


### 정리

LAN 어댑터의 MAC 회로가 공통형식의 신호를 만들고(프리앰블의 맨 앞부터 1비트씩 디지털 데이터를 전기신호로 변환) ⇒ 이를 PHY(MAU)회로에 보내, 해당 회로가 실제로 신호를 케이블에 송출할 수 있는 형식으로 변환후 케이블에 송신함.

PHY(MAU)회로 MAC회로에서 받은 신호를 케이블에 송신할 , 단지 송신 동작만 실행하는 것이아니라, 수신 신호선에서 신호가 흘러들어오는지도 감시하는 역할도 한다.

- 송신 개시 저에 신호가 흐르지 않는 것을 활인 한 후, 송신동작에 들어간다
- 신호 송신을 완료할 때까지 수신 신호가 들어오지 않으면 송수신 동작이 끝난다.

이더넷은 송신한 신호가 상대에게 완전하게 도착했는지 확인하지 않는다.

- 기기와 기기사이 케이블 길이를 100m이내, 오류가 좀처럼 발생하지 않기 떄문이다.
- 만일, 오류가 발생하면 프로토콜 스택의 TCP가 검출한다.

**신호를 송신하고 있는 사이, 수신 신호가 흘러들어오는 경우가 있다.**

- 복수의 기기가 동시에 송신 동작에 들어갈 가능성

  기기가 보낸 신호가 수신신호선으로 들어오는 것이다.

- 리피터 허브를 사용한 반 이중 모드의 경우, 이러한 상황에서 **서로의 신호가 뒤섞여 분간할 수 없는 상태**인 **충돌**이 발생한다.

  송신을 계속해도 의미가 없으므로, 이 경우 송신을 중지

- 충돌이 일어난 사실을 다른 기기에 알리기 위해 재밍 신호를 잠시동안 흘림

  기다렸다가 다시 송신 시도

- 충돌을 일으킨 기기의 대기 시간이 중복되지 않도록 고안되어있음(MAC 주소를 바탕으로 난수 생성 후 대기 시간 계산)
-
- 이더넷이 혼잡해지면 충돌의 가능성이 높아짐
    - 또 다시 충돌이 나면 대기시간이 2배로 늘어남(충돌이 발생할 때마다 2배씩 대기시간 증가)
    - 10번 다시 보내도 해결 안되면 오류로 판단

또 다른 반 이중 모드는 **송신과 수신을 동시에 실행해도 충돌 일어나지 않음**

- 수신 신호선에서 신호가 흘러와도 단순히 신호를 보내기만 하면 됨

## 10. 돌아온 패킷을 받는다.

> 리피터 허브를 이용한 반이중 동작의 이더넷에서는 누군가 신호를 보내면 리피터 허브에 접속된 케이블 전부에 흘러간다. 누군가 신호를 보내면 전부 수신 신호선에 흘러가는 것.
= 수신 동작은 이러한 신호를 전부 받아들이는 것
>

### 신호 수신

- 신호의 맨 앞에 프리앰블, 타이밍을 계산하여 스타트 프레임 딜리미터가 나오면, 그 다음 비트부터 디지털 데이터로 변환하는 동작을 개시
- 변환 동작은 송신과 반대로 PHY 회로에서 MAC 회로 방향으로 진행
    - 공통 형식 신호로 변환
    - 디지털 데이터로 변환하여 버퍼 메모리에 저장

- 신호의 마지막에 이르면, FCS 검사 진행
    - 패킷의 맨 앞부터 계산 식 적용하여 FCS값 계산하고, 이것을 패킷 끝의 FCS값과 비교한다
    - 파형이 흐트러지면 둘의 값이 일치하지 않음 ⇒ 오류 패킷으로 간주하고 폐기

- FCS에 문제가 없으면, MAC 헤더 수신처 MAC 주소와 LAN 어댑터의 MAC 주소 비교
    - 나에게 보낸게 아니면 폐기, 맞으면 받아서 버퍼 메모리에 저장
    - 할 일 끝나면 패킷을 수신한 사실을 컴퓨터 본체에 통지

### 인터럽트 구조

> 패킷을 수신한 사실을 컴퓨터 본체에 통지할 때는 인터럽트 구조를 사용한다.
>
>
> LAN 어댑터에서 알리지 않으면 컴퓨터 본체는 패킷이 도착한걸 모른다.
>
> 이 때 컴퓨터 본체가 실행하고 있는 작업에 끼어들어 LAN 어댑터쪽에 주의를 시키는 것이 인터럽트이다.
>

### 인터럽트 동작

- LAN 어댑터가 ~~확장 버스 슬로 부분에 있는~~ 인터럽트용 신호선에 신호 송신
- 신호선은 인터럽트 컨트롤러를 통해 CPU와 연결되어 있다.
- 신호가 흘러오면 CPU는 실행하던 작업을 보류하고 OS 내부의 인터럽트 처리용 프로그램으로 전환한다.
- LAN 드라이버가 호출되어 LAN 어댑터를 제어하면서 송수신 동작을 실행한다.

### 인터럽트 번호

- 인터럽트에는 번호가 할당되어 있으며, LAN 어댑터를 설치할 때 번호를 하드웨어로 설정한다.
- 인터럽트 처리용 프로그램에는 하드웨어의 인터럽트 번호에 대응하도록 드라이버 소프트웨어를 등록하게 되어 있다.
- 현재는 PnP(Plug and Play) 사양에 따라 번호를 자동으로 설정

### 인터럽트 이후

- 인터럽트에 의해 LAN 드라이버 동작
- LAN 어댑터의 버퍼 메모리에서 패킷 추출
- LAN 드라이버는 MAC 헤더의 타입 필드 값에서 프로토콜 판별 (예: TCP/IP)
- 타입 필드의 값에 대응하는 프로토콜 스택에 패킷 전달
- 프로토콜 스택이 대응하는 애플리케이션을 판단하여 적절한 조치를 취함

## 11. 서버의 응답 패킷을 IP에서 TCP로 넘긴다.

> IP 담당 부분은 IP 헤더를 통해 수신처 IP를 조사한다.
>
>
> 수신처 IP 주소와 수신한 LAN 어댑터에 할당된 주소가 일치는지 확인하고 패킷 수신
>

### ICMP

이 때, 만약 수신처 IP 주소가 다르다면 오류가 있는 것이다.

이러한 경우 IP 담당 부분이 **ICMP**라는 메세지를 통신 상대에게 보내 오류를 통지한다.

ICMP에는 여러 타입의 메세지가 정의되어 있다.

### 리어셈블링

> IP 프로토콜에는 조각 나누기(fragmentation)라는 기능이 있다.
>
>
> 패킷 운반 도중 짧은 패킷만 다룰 수 있는 통신 회선과 LAN이 있다.
>
> 이 때, 패킷을 여러 개로 분할 하는 경우가 있다.
>
> 수신한 패킷이 분할된 것이면 IP 담당 부분은 이를 되돌린다.
>
- 분할된 패킷은 IP 헤더에 있는 플래그라는 항목을 보면 알 수 있다.
- 수신 패킷이 분할된 것이라면 IP 담당 부분 내부 메모리에 일시적으로 보관한다.
- 분할된 IP 헤더에 있는 ID 정보의 값이 모두 같아 이를 참조해 다른 패킷을 기다린다.
- 프래그먼트 오프셋(fragment offset)에는 패킷이 원래 패킷의 위치 정보가 들어있다.
- 분할된 패킷이 모두 도착하면 리어셈블링을 진행한다.
    - 패킷을 원래 모습으로 되돌리는 동작

### 리어셈블링 이후

- 리어셈블링이 끝나면 패킷을 TCP 담당부분에 건내준다.
- IP 헤더에 기록된 수신처 IP, 송신처 IP, TCP 헤더에 기록된 수신처 포트 번호, 송신처 포트 번호를 조사해서 해당하는 소켓을 찾는다.
- 해당 소켓을 찾은 후에는 통신 진행 상태 기록을 기반으로 상황에 따라 적절한 동작을 실행한다.
