#  Chapter 01 Summary  
> 웹 브라우저가 메시지를 만든다.  

클라이언트는 요청을 위해 HTTP의 메시지를 만들고 **OS**에게 메시지 전송을 의뢰한다.  
이 때, URL 안에 쓰여 있는 **서버의 도메인명에서 IP 주소를 알아내야 한다**.

## Socket 라이브러리
**Socket 라이브러리**가 **IP 주소를 찾는 기능**을 제공한다.    
브라우저가 DNS 서버에 수신측 IP 주소를 확인하는데, 이렇게 요청할 수 있는 이유는 **DNS 리졸버** 덕분이다.    
브라우저는 리졸버를 통해 IP 주소를 추출하여 HTTP 요청 메세지와 함께 OS에 송신을 의뢰한다.  

### DNS 서버에 IP 주소를 요청하는 과정
* 브라우저가 리졸버 호출
* 애플리케이션 동작 일시 정지 후 리졸버 우선 실행
* 리졸버가 DNS 서버에 문의하기 위한 메시지 생성
* OS 내부 프로토콜 스택을 호출해서 (DNS 서버로) 송수신 의뢰   
* DNS 서버에서 메시지 내용에 따라 응답
* 프로토콜 스택을 통해 응답을 받은 리졸버는 IP 주소를 추출해 브라우저에게 전달

### 조회 메세지
DNS 서버는 조회 메시지와 일치하는 **리소스 레코드**를 조회한다.  
DNS 서버에 문의를 위해 생성하는 메시지는 **3가지 정보(이름, 클래스, 타입)**를 포함해야 한다.   


## DNS 서버 
도메인명에 맞는 IP 주소를 응답하기 위해 **DNS 서버는 웹 서버가 등록된 DNS 서버를 찾는다**.   

### DNS 서버 등록 방식 
먼저 하위의 도메인을 담당하는 DNS 서버의 IP 주소를 그 상위의 DNS 서버에 등록한다.  
상위의 DNS 서버를 또 그 상위의 DNS 서버에 등록하는 식으로 차례대로 등록한다.  

### DNS 서버 찾기  
* 클라이언트의 가장 가까이에 있는 DNS 서버에 웹 서버(`www.naver.com`)에 관한 정보를 조회  
  (클라이언트의 TCP/IP 설정 항목의 DNS 서버로 설정되어 있는 DNS 서버)  
* 가장 가까운 DNS 서버(이하 A 서버)에 `www.naver.com` 가 등록되어 있지 않으면, A 서버가 루트 도메인 DNS 서버에 조회메세지를 전송
* 루트 도메인 DNS 서버에 com 가 등록되어 있으므로 com 의 DNS 서버 IP 주소를 반송
* `naver.com` 에서도 같은 과정을 반복
* `www` 에 도달하면 클라이언트에 회답할 항목을 받음  

### DNS 서버의 캐시 기능
DNS 서버는 한 번 조사한 이름을 캐시에 기록.  
조회한 이름이 도메인에 등록되어 있지 않은 경우에는 이름이 존재하지 않는다는 회답이 돌아오지만, 이 역시 캐시에 기록.  

## 프로토콜 스택 
> 애플리케이션은 데이터 송수신을 프로토콜 스택에 의뢰한다. 
> 데이터 송수신을 위해서 출입구(소켓)가 필요하다.  

* `소켓 작성`: 서버측에서 소켓(데이터의 출입구) 생성하고, 소켓에 클라이언트의 파이프 연결을 기다린다.  
* `접속`: 클라이언트측에서 소켓을 생성하고, 파이프를 늘려 서버측의 소켓에 연결한다. 
* `송수신`: 소켓이 서로 연결되면 데이터 송 수신 동작이 실행된다. 
* `연결 끊기`: 데이터를 전부 보내고 나면 파이프가 분리 되고, 소켓을 말소하여 통신을 종료한다.

### 소켓 작성
* 애플리케이션이 소켓 라이브러리의 소켓 프로그램을 호출
* 소켓 생성 시, 디스크립터(번호표, 소켓 식별 역할) 생성 및 반환

### 파이프 연결 (접속)
* 소켓을 서버측의 소켓에 접속하도록 프로토콜 스택에 의뢰 
* 애플리케이션이 소켓 라이브러리의 connect라는 프로그램을 호출
* connect를 호출하면, 프로토콜 스택이 파이프 연결(접속 동작)을 실행
* connect를 호출할 때 디스크립터, 서버의 IP주소, 포트번호를 지정
* 연결된 상대의 ip 주소나 포트번호 등의 정보를 소켓에 기록

### 데이터 송수신
> 애플리케이션이 소켓 라이브러리의 `write`라는 프로그램 부품을 사용해 프로토콜 스택이 데이터 송 수신 하도록 함  

#### 데이터 송 수신 과정
1. 애플리케이션이 송신 데이터를 메모리에 준비  
2. 소켓라이브러리의 `write`라는 프로그램 부품을 호출 (디스크립터, 송신 데이터를 지정)  
3. 프로토콜 스택이 송신 데이터를 서버에 송신
4. 서버는 수신동작을 실행 (데이터 내용 조사 및 처리 후 응답 메시지 반송)
5. 응답 메시지가 돌아오면, 메시지를 수신
6. 소켓 라이브러리의 `read`라는 프로그램 부품을 호출하여 프로토콜 스택에 수신 동작을 의뢰
7. `read`가 응답메시지를 **수신 버퍼에 저장**  



