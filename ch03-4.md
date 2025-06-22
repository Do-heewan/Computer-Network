# 혼잡 제어 및 TCP 진화

<br>

## Principles of Congestion Control (혼잡 제어의 기본 개념)

### Congestion(혼잡)
- 정의 : 너무 많은 송신자가 네트워크가 처리할 수 있는 것보다 빠르게 데이터를 전송하는 상황
- 현상
    - 라우터 버퍼에서의 긴 지연
    - 버퍼 오버플로우로 인한 패킷 손실

- 흐름 제어와의 차이 : 흐름 제어는 하나의 송신자가 하나의 수신자에게 너무 빠르게 전송하는 문제

### 혼잡의 원인과 비용

**시나리오 1** : 무한 버퍼
- 최대 연결당 처리량 R/2
- 도착률이 R/2에 접근하면 지연이 급격히 증가

![Image](https://github.com/user-attachments/assets/25aed703-1ccd-41c1-8561-550b010fb8d2)

**시나리오 2** : 유한 버퍼 + 재전송

- 이상적 상황 : 완벽한 지식으로 버퍼가 가용할 때만 전송
- 현실적 상황 : 불필요한 중복 재전송으로 인한 용량 낭비

![Image](https://github.com/user-attachments/assets/e392c42b-a7e9-488d-978e-51b0cfe540df)

**시나리오 3** : 다중 홉 경로
- 추가 비용 : 상위 스트림에서 사용된 전송 용량과 버퍼링이 하위 스트림에서 패킷이 드롭되면 낭비됨

![Image](https://github.com/user-attachments/assets/5deae8fc-6d33-45cb-8aa6-57862c57672c)

<br>

## 혼잡 제어 접근법

### End-to-End 혼잡 제어 (TCP 방식)
- 네트워크로부터 명시적 피드백 없음
- 관찰된 손실과 지연으로부터 혼잡 추론

### Network-Assisted 혼잡 제어
- 라우터가 송신/수신 호스트에게 직접 피드백 제공
- 혼잡 수준 표시 또는 명시적 전송률 설정

<br>

## TCP 혼잡제어 : AIMD

### AIMD (Additive Increase Multiplicative Decrease)
- 가산 증가 : 손실 감지까지 RTT마다 1 MSS씩 전송률 증가
- 곱셈 감소 : 손실 이벤트마다 전송률을 절반으로 감소
- 특징 : 톱니바퀴 형태의 대역폭 탐색 활동

### TCP 세부 구현
- slow start : 연결 시작 시 지수적 증가 (cwnd = 1 MSS부터 시작)
- congestion avoidance : ssthresh 이후 선형 증가
- fast recovery : 3번의 중복 ACK시 빠른 복구

![Image](https://github.com/user-attachments/assets/1e7fb4b3-77a6-4d0e-875d-92dc4a8b4da6)

<br>

## TCP의 발전

기존 AIMD 보다 더 나은 대역폭 탐색 방법 필요

### TCP CUBIC
- 손실 발생 지점(Wmax)을 기억
- K 지점까지의 거리에 따라 3제곱 함수로 증가
- Linux 기본값, 인기 웹 서버에서 사용

### 지연 기반 TCP 혼잡 제어

"just full enough, but no fuller"

- RTTmin 측정
- 측정된 처리량과 비혼잡 처리량 비교
- 처리량에 따라 cwnd 선형 조정

**장점** : 손실 유발 없는 혼잡 제어, 높은 처리량과 낮은 지연 동시 달성

### ECN (Explicit Congestion Notification)
- IP헤더의 2비트를 사용해 네트워크 라우터가 혼잡 표시
- 목적지가 ACK 세그먼크의 ECE 비트로 송신자에게 혼잡 알림

<br>

## TCP 공정성

### 공정성 목표
- K개의 TCP 세션이 대역폭 R인 병목 링크를 공유할 때, 각각 평균 R/K 속도를 가져야 함

### 공정성 달성

- 이상적 조건 하에서: 동일한 RTT, 고정된 세션 수, 혼잡 회피 모드에서만 동작
- 현실적 문제:
    - UDP 사용 멀티미디어 앱 (혼잡 제어 미사용)
    - 병렬 TCP 연결 사용 (더 많은 대역폭 확보)

<br>

## 전송 계층 기능의 진화

### QUIC (Quick UDP Internet Connections)

UDP 위의 애플리케이션 계층 프로토콜

- 장점
    - 1 RTT로 연결 설정 (TCP+TLS는 2번의 핸드셰이크)
    - 다중 애플리케이션 스트림 멀티플렉싱
    - HOL(Head-of-Line) 블로킹 방지
    - 별도의 신뢰성 있는 데이터 전송과 보안


사용: Google 서버, Chrome, 모바일 YouTube 앱에 배포