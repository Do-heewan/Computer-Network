# 프로토콜이란?
- 네트워크 프로토콜 : 인터넷에서 프로토콜에 의해 지배되는 모든 소통 활동

### Internet Protocol Stack
- Application Layer : 애플리케이션을 직접적으로 지원하는 계층. Transport Layer에 직접적인 영향
- Transport Layer : process와 process 간의 데이터 전송 지원 계층
- Network Layer : 소스로 부터 목적지까지 데이터그램을 라우팅
- Link Layer : 이웃한 네트워크로 데이터 전송
- Physical Layer : 비트 단위로 전송

<br>

# Internet Structure
#### Network Edge
- host : 클라이언트, 서버
- 서버는 데이터 센터에 있다.

#### Access networks
- 유무선 link
- edge(host)와 바로 붙어있는 네트워크

#### Network core
- 중심부에 있는 라우터와 인접해있다.
- 네트워크의 네트워크

### 엔드 시스템은 엣지 라우터에 어떻게 연결할까?
- Mobile Access Networks (WiFi, 4G/5G)

#### 우리가 봐야할 것
- Access Network의 전송률 (bits per second)
- 유저간 전용 액세스 또는 공유 액세스?

<br>

# 무선 액세스 네트워크
- 공유 무선 액세스 네트워크는 앤드 시스템을 통해 라우터에 연결된다.

#### 무선 로컬 지역 네트워크(WLANs)
- 짧은 거리 통신
- 속도는 그만큼 빠르다. (450Mbps, 10+Gbps의 전송률)
#### 셀룰러 액세스 네트워크
- 셀룰러 네트워크 오퍼레이터로 인해 제공된다. (10Km 이상)
- 속도 차이가 크다 (10s ~ 1000 Mbps)
- 4G/5G 셀룰러 네트워크

# 네트워크 코어
- 인접연결된 라우터들의 mesh
### Packet Switching 
- Host는 Application Layer에서 사용자의 메세지를 패킷 단위로 처리한다.
### Store and Forward
- Source에서 L bits per packet의 속도로 메세지가 전달.
- Link는 R bps의 전송률을 가지고 있음.
- 라우터는 L/R의 속도로 하나의 패킷을 전송 받음. (**Transmission Delay**)
- 라우터는 하나의 패킷은 온전히 받을 때 까지 저장. 이후 전송
- End-end delay : 2L/R(전파 지연은 0이라 가정)
### Queueing Delay, Loss
- 여러개의 Host에서 패킷 단위로 메세지를 받을 때, 라우터 내부에는 Queue로 패킷을 저장.
- 패킷이 들어오는 시간보다 나가는 시간이 더 느릴때 발생하는 딜레이.

# 네트워크 코어의 두 키 함수
### Routing
- 글로벌 액션
- 패킷이 어디로 이동할 지 경로를 생성
- Routing Algorithm
	- forward table 제작
### Forwarding
- 로컬 액션
- 라우팅 알고리즘에 의해 생성된 Forward Table을 이용해 패킷을 이동

# Packet Delay
- nodal processing : 비트 에러 체크, link까지 도달하는데 걸리는 시간
- queueing delay : output link에서 전송에 걸리는 시간, 라우터의 혼잡도 레벨에 의존
- transmission delay : 패킷의 길이에 따른 링크의 전송률
- propagation delay : 물리적인 링크의 길이에 따른 속도
### Packet Loss
- 패킷은 라우터 내부에 FIFO 방식으로 순위를 가지며 전송됨
- 라우터가 가득차면 패킷을 일부 버린다 (Loss)
- 소실된 패킷들은 시스템에 따라 재전송 할수도
# Throughput
- Sender에서 Receiver로 전달되는 비트의 rate