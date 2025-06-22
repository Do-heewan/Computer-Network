# Transport services and protocols
- application process와 다른 실행중인 호스트 간의 logical communication을 제공한다. 
- Transport Protocols의 동작
	- sender : Application message를 세그먼트 단위로 나누어 Network Layer로 전달
	- receiver : 전달받은 세그먼트를 하나로 모아 Application Layer로 전달
- 인터넷 응용 프로그램에서 사용 가능한 두 개의 전송 프로토콜 : TCP, UDP

# Transport Layer와 Network Layer의 차이
### Network Layer
- Host 간 논리적 소통
### Transport Layer
- 프로세스 간 논리적 소통 - Network Layer 서비스를 활용, 향상 시킴
### 가정집 비유
	호스트  = 집
	프로세스 = 아이들
	Application Message = 편지
	Transport Protocols = 편지를 전달하는 Ann과 Bill
	Network Protocol = 우편 서비스(우편함 등)

# 주요 Transport Protocols
### TCP (Transmission Control Protocol)
- 신뢰성 있는 순서대로 전송
- 혼잡 제어
- 흐름 제어
- 연결 설정
### UDP (User Datagram Protocol)
- 비신뢰적, 비순서적 전송
- IP의 "best-effort" 확장
- 지연 보장, 대역폭 보장에 대해 서비스가 제공되지 않는다.

# Multiplexing & Demultiplexing

### Multiplexing
- 여러 소캣에서 데이터 handle, transport 헤더 추가
	- 가장 최근 demultiplexing에 사용된 헤더
### Demultiplexing
- 전달받은 세그먼트들을 header를 이용해 올바른 소캣에 사용한다.

### Demultiplexing 동작 방식
- Host는 IP 데이터그램을 전달 받는다.
	- 각 데이터그램에는 IP 주소, 목적지 IP 주소가 있다.
	- 각 데이터그램에는 하나의 transport-layer 세그먼트가 있다.
	- 각 세그먼트는 source, 목적지 포트 번호가 있다.
- Host는 IP 주소와 포트 번호를 이용해 세그먼트를 적절한 소캣으로 지시한다.

### UDP : Connectionless Demultiplexing
- UDP 소캣으로 전송할 때, 도착 IP 주소와 port # 이 있어야 한다.
- host가 UDP 세그먼트를 전송 받으면,
	- port # 목적지 확인
	- 알맞는 port # 의 socket으로 전달
- IP/UDP 데이터그램은 같은 목적지 port #, 하지만 다른 IP 주소 or Port 번호는 같은 소캣의 호스트가 전달 받음

### TCP : Connection Oriented Demultiplexing
TCP 소켓은 4개의 tuple로 식별
	- source IP address
	- source port number
	- dest IP address
	- dest port number

demux : 4개의 value를 이용

server는 많은 TCP 소캣 지원
	각 소켓은 그들의 4-tuple에 의해 식별
	각 소캣은 다른 클라이언트와 연결


# Connectionless Transport : UDP
