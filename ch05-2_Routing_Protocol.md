# 네트워크 계층 라우팅 프로토콜

<br>

## 확장 가능한 라우팅의 필요성

### 문제점

- 규모 : 수십억 개의 목적지를 모든 라우팅 테이블에 저장할 수 없음
- 관리적 자율성 : 각 네트워크 관리자가 자신의 네트워크 내에서 라우팅을 제어하고 싶어함

### 해결책 : 자율 시스템 (AS, Autonomous System)
- 라우터를 "자율 시스템" 또는 "도메인"이라고 하는 영역으로 그룹화
- 계층적 라우팅 구조를 통해 확장성 확보

<br>

## 라우팅의 두 가지 유형

### Intra-AS 라우팅 - 도메인 내 라우팅
- 정의 : 같은 AS 내에서의 라우팅
- 특징
    - AS 내 모든 라우터는 동일한 intra-domain 프로토콜 실행
    - 다른 AS는 서로 다른 intra-domain 라우팅 프로토콜 사용 가능

### Inter-AS 라우팅 - 도메인 간 라우팅
- 정의 : AS들 간의 라우팅
- 특징 : 게이트웨이 라우터가 inter-domain과 intra-domain 라우팅 모두 수행

<br>

## 주요 Intra-AS 라우팅 프로토콜

### RIP (Routing Information Protocol)
- 클래식 Distance Vector 알고리즘
- 30초마다 DV 교환
- 현재는 널리 사용되지 않음

### EIGRP (Enhanced Interior Gateway Routing Protocol)
- Distance Vector 기반
- 이전에는 Cisco 독점 프로토콜

### OSPF (Open Shortest Path First)
링크 상태 라우팅 프로토콜

- 특징
    - 각 라우터가 전체 AS에 OSPF 링크 상태 광고를 뿌림
    - 다양한 링크 비용 메트릭 지원 (대역폭, 지연)
    - 다익스트라 알고리즘으로 포워딩 테이블 계산
    - 보안 : 모든 OSPF 메세지 인증

### 계층적 OSPF
- 2단계 계층 : 로컬 영역과 백본
- 영역 경계 라우터 : 자신의 영역 내 목적지까지의 거리를 요약하여 백본에 광고
- 백본 라우터 : 백본에 제한된 OSPF 실행
- 경계 라우터 : 다른 AS에 연결

<br>

## Inter-AS 라우팅 : BGP (Border Gateway Protocol)

### BGP의 역할
- 인터넷을 하나로 묶는 접착제
- 서브넷이 자신의 존재와 도달 가능한 목적지를 인터넷에 광고
- AS에게 다음 기능 제공
    - eBGP : 인접 AS로부터 서브넷 도달성 정보 획득
    - iBGP : 도달성 정보를 AS 내부 모든 라우터에 전파
    - 정책 기반 좋은 경로 결정

### BGP 연결
- eBGP 연결 : AS 간 연결
- iBGP 연결 : AS 내부 연결
- 게이트웨이 라우터는 eBGP와 iBGP 프로토콜 모두 실행

![Image](https://github.com/user-attachments/assets/f5be75ca-3da9-43f2-82fb-4e2e1b1e4033)

### BGP 기본 개념
- BGP 세션 : 두 BGP 라우터가 TCP 연결을 통해 BGP 메시지 교환
- 경로 벡터 프로토콜 : 다양한 목적지 네트워크 프리픽스에 대한 경로 광고

### BGP 경로 속성
- AS-Path : 프리픽스 광고가 통과한 AS들의 목록
- NEXT-HOP : 다음 홉 AS로의 특정 내부 AS 라우터 지시

### BGP 메시지 유형
- OPEN : 원격 BGP 피어에 TCP 연결 열기 및 인증
- UPDATE : 새 경로 광고 또는 기존 경로 철회
- KEEPALIVE : 업데이트가 없을 때 연결 유지
- NOTIFICATION : 오류 보고 및 연결 종료

### BGP 경로 선택 기준
1. 로컬 선호도 값 : 정책 결정
2. 최단 AS-PATH
3. 가장 가까운 NEXT-HOP 라우터 : 핫 포테이토 라우팅

<br>

## 정책 기반 라우팅

### BGP를 통한 정책 구현
- 수입 정책 : 게이트웨이가 경로 광고를 수락/거부 결정
- AS 정책 : 다른 인접 AS에 경로를 광고할지 결정

### 핫 포테이토 라우팅
- 가장 적은 intra-domain 비용을 가진 로컬 게이트웨이 선택
- inter-domain 비용은 고려하지 않음

<br>

## Intra-AS vs Inter-AS 라우팅

### Policy(정책)
- Inter-AS : 관리자가 트래픽 라우팅 방식과 네트워크를 통과하는 트래픽을 제어하고 싶어함
- Intra-AS : 단일 관리자이므로 정책이 덜 중요

### Scale(규모)
- 계층적 라우팅으로 테이블 크기 절약 및 업데이트 트래픽 감소