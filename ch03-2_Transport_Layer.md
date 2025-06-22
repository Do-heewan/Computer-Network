# 데이터 전송 신뢰성 원칙 (Principles of RDT)
### 신뢰성 있는 서비스 추상화
- 간단

### 서비스 구현 단계
- sender와 receiver는 신뢰성 있는 데이터를 주고 받음
- 신뢰할 수 있는 데이터 전송 프로토콜의 복잡성은 신뢰할 수 없는 채널의 특성에 따라 의존된다.
	- loss, currupt 등

**예를 들어 sender와 receiver가 message를 주고 받을 때, 서로는 서로의 State에 대해 알지 못한다.**

### interface
rdt_send() : 위에서 호출, (Application) -> 데이터를 rdt로 전송
udt_send() : rdt가 호출, packet으로 전송한다. 하위(unreliable channel)로 전송
rdt_rcv() : 채널에서 패킷이 도착하여 receiver로 보내야 할 때 호출
deliever_data() : rdt에서 호출, data를 상위 Layer로 전달하기 위해

## 신뢰성 있는 데이터의 전송
- Sender와 Receiver 사이의 RDT protocol을 개발,
- 데이터의 전송은 단방향으로 지정
	- 하지만 제어 정보(control info)는 양방향
- Finite State Machine(FSM)을 사용하여 sender와 receiver 특정

![[Pasted image 20250416195551.png]]
현재 state에서 다음 state는 event에 의해 정의된다.
한 event에 대해서 한 state로만 전의된다.

---
# rdt1.0
![[Pasted image 20250416200133.png]]
- channel의 완벽한 신뢰성
	- no bit errors
	- no loss of packets
- FSM의 sender와 receiver의 구분
	- sender는 channel에게 데이터를 보냄
	- receiver는 channel에서 데이터를 읽음



# rdt2.0
- underlying channel에서 flip bits -> bit error 발생 가능성
	- checksum을 통해 bit error check
>그렇다면 에러 복구는 어떻게 할 것인가?

### acknowledgements(ACKs)
- receiver가 패킷을 온전히 전달 받았을 때 sender에게 보내는 packet
### negative acknowledgements(NAKs)
- receiver가 패킷을 온전히 전달받지 못하였을 때(전달받은 packet에 error가 있을 때) sender에게 보내는 packet

**Sender는 NAK를 받게 되면 패킷을 재전송 한다.**

### stop and wait
sender는 하나의 패킷을 보내고, receiver의 응답을 기다린다.

### 동작 원리
**Sender 동작**
1. Sender는 상위 계층의 call을 기다리는 상태
2. rdt_send(data) 실행
3. sndpkt 생성, udt_send(sndpkt) 실행
4. ACK or NAK을 기다리는 상태
5. rdt_rcv(rcvpkt) && isNAK(rcvpkt)  => receive로 부터 packet을 받았고, 받은 패킷이 NAK일 경우.
	1. udt_send(sndpkt) 실행 (데이터 재전송)
6. rdt_rcv(rcvpkt) && isACK(rcvpkt) => receive로 부터 packet을 받았고, 받은 패킷이 ACK인 경우
	1. 아무런 Action 없음
7. 1번으로 돌아감.

**Reciever 동작**
1. 하위 계층으로 부터 call을 기다리는 상태
2. rdt_rcv(rcvpkt) && corrupt(rcvpkt) => packet을 생성하였고 해당 packet에 bit error가 있는 경우
	1. udt_send(NAK) => NAK packet 전송
3. rdt_rcv(rcvpkt) && notcorrupt(rcvpkt) => packet을 생성하였고 해당 packet에 문제 없는 경우
	2. extract(rcvpkt, data) => packet에서 data 추출
	3. deliever_data(data) => data 전달
	4. udt_send(ACK) => ACK 전송
![[Pasted image 20250416203426.png]]

## rdt2.0의 작은 문제점
### ACK/NAK가 corrupt를 발생한다면?
- sender는 receiver에서 발생한 문제를 알 지 못한다.
- 데이터 중복 때문에 재전송을 할 수 없음
### Handling duplicates
- ACK/NAK에 corrupt 발생 시 sender는 패킷을 재 전송하게 되는데, 이때 패킷에 sequence number를 붙여 전송한다.
- sequence number를 통해 receiver는 패킷의 중복을 확인할 수 있다.



# rdt2.1
**Sender 동작**

**Receiver 동작**

### discussion
**Sender**
- 패킷에 seq num을 붙임 [0, 1]
- ACK/NAK를 전달받았다면 corrupted를 체크해야 함
- state의 수가 두 배가 됨
**Receiver**
- 패킷의 중복을 확인해야 함
	- seq num 0, 1 비교
- receiver는 sender가 ACK/NAK를 잘 받았는지 알 지 못한다.



# rdt2.2
- rdt2.1과 동일한 동작, ACK만 사용한다.
- receiver는 패킷 전달을 받고 나면 NAK 대신에 ACK를 보낸다.
	- ACK에 seq num을 포함하여 전송한다.
- sender에서 중복된 ACK는 NAK처럼 동작한다. : 패킷 재전송

### Sender와 Receiver 동작




# rdt3.0
- underlying channel에서 패킷을 소실할 수도 있다. (data, ACKs 등)
	- checksum, sequence number, 중복 ACK, 재전송 등이 있지만 충분하지 않다.

### 해결법
- sender는 ACK를 적절한 시간 동안 기다린다.
- 시간안에 ACK가 오지 않으면 재전송
- 만약 패킷(ACK)가 단순히 지연된거라면?
	- 재전송시 중복이 발생, 하지만 seq num을 이미 포함하였기에 이로 식별
- countdown timer를 사용하여 적절한 시간이 지나면 interrupt 시킨다.

### 동작


### 성능
- $U_{sender}$ : utilization - 전체 시간중에 sender가 동작한 시간
### example



# Go-Back-N
### sender
sender는 Ack를 받지 않은 패킷에 대해 N개의 연속적인 패킷을 보낼 수 있다. (window size N) 
![[Pasted image 20250418002516.png]]
- cumulative ACK : seq num `#`을 포함한 모든 패킷에 대해 ACK
	- ACK를 받으면 window 한칸 이동
- 타이머는 마지막에 전송된 패킷 기준
- timeout : window 내 가장 높은 seq number의 패킷을 재전송

### receiver
ACK-only : seq num이 가장 크고 전송을 완전히 받은 패킷에 대해 ACK를 보냄
- ACK의 중복 가능성
- 이를 해결하기 위해 `rcv_base`에서 기억
