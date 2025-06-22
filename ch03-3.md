# TCP

<br>

## TCP 개요

- point-to-point : 하나의 송신자와 하나의 발신자
- 신뢰성 있는 순서 보장
- Full duplex data(전이중 통신) : 같은 연결에서 양방향 Data Flow 가능
- cumulative ACKs
- pipelining : TCP 혼잡 및 흐름 제어를 통한 윈도우 크기 설정
- connection-oriented (연결 지향적) : 데이터 교환 전 handshaking을 통해 연결 설정
- Flow Controlled (흐름 제어) : 송신자가 수신자를 압도하지 않도록 제어

<br>

## TCP 세그먼트 구조

![Image](https://github.com/user-attachments/assets/a6a29501-5322-4cf1-b2c9-133f7f6c904a)

<br>

## TCP Sequence Numbers, ACKs

### 시퀀스 넘버
- 세그먼트 데이터의 첫 번째 바이트에 대한 바이트 스트림 번호
- 세그먼트 단위가 아닌 바이트 단위로 계산

### ACK
- 상대방으로 부터 기대되는 다음 바이트의 시퀀스 넘버
- cumulative ACKs (누적 ACKs) : 해당 번호까지의 모든 바이트가 정확히 수신되었음을 의미
- 순서가 맞지 않는 세그먼트는 구현에 따라 다름

<br>

## TCP 송신자 동작

<br>

## TCP 라운드 트립 시간 (RTT) 및 타임아웃

EstimatedRTT = (1-α) × EstimatedRTT + α × SampleRTT
(일반적으로 α = 0.125)

### 타임아웃 간격 설정

TimeoutInterval = EstimatedRTT + 4 × DevRTT

- DevRTT: SampleRTT와 EstimatedRTT 간 편차의 EWMA
- EstimatedRTT의 변동이 클수록 더 큰 안전 여유 필요

<br>

## TCP 수신사 : ACK 생성 규칙

|수신자에서의 이벤트|TCP 수신자 동작|
|---|----|
|기대 시퀀스 번호의 순서대로 세그먼트 도착, 모든 데이터가 이미 ACK됨|지연된 ACK. 다음 세그먼트를 최대 500ms 대기. 없으면 ACK 전송|
|기대 시퀀스 번호의 순서대로 세그먼트 도착, 하나의 ACK가 대기 중|즉시 단일 누적 ACK 전송|
|기대보다 높은 시퀀스 번호의 순서 틀린 세그먼트 도착|즉시 중복 ACK 전송|
|간격을 부분적/완전히 채우는 세그먼트 도착|세그먼트가 간격의 하위 끝에서 시작하는 경우 즉시 ACK 전송|

<br>

## TCP 재전송 시나리오

![Image](https://github.com/user-attachments/assets/14b913a5-d6cd-405b-b009-3a2d1c121ffe)

### 주요 시나리오

1. ACK 손실: 타임아웃 후 재전송
2. 조기 타임아웃: 늦게 도착한 ACK로 인한 불필요한 재전송
3. 누적 ACK: 이전 손실된 ACK를 나중 ACK가 커버

### 빠른 재전송 (Fast Retransmit)

1. 같은 데이터에 대해 3개의 중복 ACK 수신 시
2. 타임아웃을 기다리지 않고 즉시 재전송
3. 세그먼트 손실 가능성이 높다고 판단

<br>

## TCP 흐름 제어

### 목적

네트워크 계층이 애플리케이션 계층보다 빠르게 데이터를 전달할 때 수신자 버퍼 오버플로우 방지

### 매커니즘

- rwnd(receive window) : TCP 헤더에서 수신자가 사용 가능한 버퍼 공간 광고
- 송신자 제한 : 미확인 데이터량을 수신된 rwnd로 제한
- RcvBuffer : 소켓 옵션으로 설정 (기본값 4096 바이트), 많은 OS에서 자동 조절

<br>

## TCP 연결 관리

### 2-way 핸드셰이크의 문제점

- 가변 지연, 메시지 손실로 인한 재전송, 메시지 순서 변경
- 반개방 연결 (half-open connection) 문제
- 중복 데이터 수용 문제

### 3-way 핸드셰이크

1. 클라이언트: SYN=1, Seq=x 전송
2. 서버: SYN=1, ACK=1, Seq=y, ACKnum=x+1 전송 (SYNACK)
3. 클라이언트: ACK=1, ACKnum=y+1 전송 (클라이언트-서버 데이터 포함 가능)

### 연결 종료

- 클라이언트와 서버가 각각 연결의 자신 쪽을 종료
- FIN 비트 = 1인 TCP 세그먼트 전송
- 수신된 FIN에 대해 ACK로 응답
- 동시 FIN 교환도 처리 가능