# 네트워크 계층 제어 평면

<br>

## Software Defined Networking(SDN)

### SDN의 배경
- 기존 방식의 한계 : 분산된 라우터별 제어 방식으로 관리가 복잡하고 트래픽 엔지니어링이 어려움

### SDN의 핵심 배경
- 논리적으로 중앙집중화된 평면
- 제어 평면과 데이터 평면의 분리
- 프로그래밍 가능한 네트워크 제어

### SDN의 장점
1. 쉬운 네트워크 관리 : 라우터 설정 오류 방지, 트래픽 흐름의 유연성 증대
2. 테이블 기반 포워딩 : OpenFlow API를 통한 라우터 프로그래밍 가능
3. 오픈 구현 : 비독점적 제어 평면으로 혁신 촉진

### SDN 아키텍쳐 구성요소
1. 데이터 평면 스위치
    - 빠르고 단순한 상용 스위치
    - 하드웨어에서 일반화된 데이터 평면 포워딩 구현
    - 컨트롤러 감독 하에 플로우 테이블 계산 및 설치

![Image](https://github.com/user-attachments/assets/64ecc20f-31f3-4d1d-af3e-1b3e3001e706)

2. SDN 컨트롤러 (네트워크 OS)
    - 네트워크 상태 정보 유지
    - Northbound API를 통해 상위 네트워크 제어 애플리케이션과 상호작용
    - Southbound API를 통해 하위 네트워크 스위치와 상호작용
    - 성능, 확장성, 내결함성을 위한 분산 시스템으로 구현

![Image](https://github.com/user-attachments/assets/6858ecb1-6cae-45f6-90a3-84335b712271)

3. 네트워크 제어 애플리케이션
    - 제어 기능의 두뇌 역할
    - SDN 컨트롤러가 제공하는 하위 수준 서비스와 API 사용
    - 제3자 제공 가능

![Image](https://github.com/user-attachments/assets/cab86f50-ab8f-4c37-9b44-8633563c8d25)

<br>

## OpenFlow 프로토콜

### 기본 특징
- 컨트롤러와 스위치 간 통신
- TCP 사용, 선택적 암호화
- OpenFlow API와는 구별됨

### 메세지 유형
1. Controller-to-Switch
2. Switch-to-Controller
3. Symmetric

<br>

## SDN 컨트롤러 구현 사례

### OpenDayLight(ODL)
- Service Abstraction Layer(SAL)을 통한 내부/외부 애플리케이션 연결
- 다양한 southbound 프로토콜 지원 (OpenFlow, NETCONF, SNMP)

### ONOS
- 분산 코어에 중점
- Intent 프레임워크 : 서비스의 고수준 명세
- 제어 앱과 컨트롤러 분리

<br>

## ICMP (Internet Control Message Protocol)

### 기능
- 호스트와 라우터 간 네트워크 수준 정보 통신
- 오류 보고 : 도달 불가능한 호스트, 네트워크, 포트, 프로토콜
- Echo request / reply (ping 사용)

### 주요 메시지 타입

- Type 0: Echo reply (ping)
- Type 3: Destination unreachable
- Type 8: Echo request (ping)
- Type 11: TTL expired

### Traceroute에서의 활용
- TTL 값을 증가시키며 UDP 세그먼트 전송
- 각 라우터에서 ICMP "TTL expired" 메세지 반환
- RTT 기록을 통한 경로 추적

<br>

## 네트워크 관리

### 정의
- 하드웨어, 소프트웨어, 인적 요소의 배치, 통합, 조정을 통해 네트워크와 요소 자원을 모니터링, 테스트, 풀링, 구성, 분석, 평가, 제어하는 것

### 구성요소
- 관리 서버 : 네트워크 관리자가 함께 작동하는 애플리케이션
- 관리 장치 : 관리 가능한 하드웨어/소프트웨어 구성요소를 가진 장비
- 데이터 : 장치 상태, 구성 데이터, 운영 데이터, 장치 통계
- 네트워크 관리 프로토콜 : 서버와 장치 간 통신

<br>

## SNMP (Simple Network Management Protocol)

### 특징
- Request/Response 모드와 Trap 모드 지원
- MIB (Management Informantion Base) 사용

### 메시지 타입

- GetRequest, GetNextRequest, GetBulkRequest
- SetRequest
- Response
- Trap

### MIB (Management Information Base)

- 장치의 운영 및 일부 구성 데이터
- RFC에서 정의된 400개의 MIB 모듈
- 벤더별 MIB도 다수 존재

<br>

##  NETCONF/YANG

### NETCONF 개요
- 네트워크 전체의 능동적 장치 관리/구성
- RPC (Remote Procedure Call) 패러다임
- XML로 인코딩된 프로토콜 메세지
- 보안 및 신뢰성 있는 전송 프로토콜 사용

### 주요 NETCONF 작업

- `get-config`: 구성 검색
- `get`: 구성 상태와 운영 상태 데이터 검색
- `edit-config`: 지정된 구성 변경
- `lock`, `unlock`: 구성 데이터스토어 잠금/해제

### YANG
- NETCONF 네트워크 관리 데이터의 구조, 구문, 의미를 명시하는 데이터 모델링 언어
- 내장 데이터 타입 제공
- 유효한 NETCONF 구성이 만족해야 하는 데이터 간 제약 조건 표현 가능