## Amazon EC2

### Elastic Compute Cloud : Infrastructure as a Service

- AWS에서 제공하는 서비스형 인프라스트럭처

### EC2 의 설정 옵션

- 운영체제 : Linux, Window, Mac OS
- computer power와 core(CPU)
- RAM
- 네트워크를 통한 스토리지 여부
- 연결할 네트워크 종류
- 방화벽 규칙, 보안그룹
- **EC2 UserData** : 인스턴스를 구성하기 위한 부트스트랩 스크립트
    - EC2 User data 스크립트를 통해서 인스턴스를 부트스트랩할 수 있다
    - 머신이 작동될 때 명령을 시작. 처음 시작할 때 한 번만 실행되고 다시는 실행되지 않는다
    - `ex` 인스턴스를 부팅할 때 자동화하고 싶은 작업 ?
        - 업데이트
        - 일반적인 파일을 인터넷에서 다운로드
        - 사용자가 원하는 모든 것
    - 루트 계정에서 실행되기 때문에 모든 명령문은 sudo로 해야 한다

<br>

### EC2 instance types
### 인스턴스 유형의 뜻

![Untitled](https://user-images.githubusercontent.com/46165696/173238083-5d726677-c02f-4a7f-bef7-7ea62fb527d7.png)

### 인스턴스 종류에 따른 특징

- 범용 인스턴스
    - 웹 서버, 코드 저장소와 같은 다양한 작업에 적합
    - 컴퓨팅, 메모리, 네트워킹 간의 균형도 잘 맞음
- 컴퓨팅 최적화 인스턴스 (Compute Optimized)
    - 고성능 프로세서을 요구하는 컴퓨터 집약적인 작업에 최적화
    - 미디어 트랜스 코딩 작업 또는 고성능 웹 서버 & HPC 작업, 머신러닝, 전용 게임 서버 등에 사용
    - C 로 시작하는 인스턴스 클래스
- 메모리 최적화 인스턴스 (Memory Optimized)
    - 메모리에서 대규모 데이터 셋을 처리하는 유형의 작업에 최적화
    - 대규모 데이터셋을 처리하는 유형의 작업
    - 고성능 관계형/비관계형의 데이터베이스, 엘라스틱 캐시같은 분산 웹스케일 캐시 저장소, 대규모 비정형 데이터의 실시간 처리를 실행하는 애플리케이션에 사용
    - R 로 시작하는 인스턴스 클래스 (RAM 의 R 이나, X1, Z1 클래스도 존재)
- 스토리지 최적화 인스턴스 (Storage Optimized)
    - 로컬 스토리지에서 대규모의 데이터셋에 액세스할 때 적합한 인스턴스
    - 고주파 온라인 트랜잭션 처리(OLTP 시스템), 관계형/비관계형 데이터베이스, 레디스같은 인메모리 데이터베이스의 캐시 또는 데이터 웨어하우징 애플리케이션과 분산 파일 시스템에 사용
    - I, G, H1로 시작하는 인스턴스 클래스

<br>

---

## Security Groups

- EC2 인스턴스에 들어오고 나가는 트래픽을 제어
- EC2 인스턴스의 방화벽

허용 규칙만 포함

- 출입이 허용된 것이 무엇인지 확인 가능
- IP 주소나 다른 보안 그룹을 참조해 규칙을 만들 수 있다

### 보안그룹이 규제하는 것

- Port로의 접근
- IPv4인지 IPv6인지 확인
- 외부에서 인스턴스 안으로 들어오는 inbound network 통제
- 인스턴스에서 외부로 나가는 outbound network 통제
<img src="https://user-images.githubusercontent.com/46165696/173852495-8948c849-b0c6-4272-b74c-3b3abb4b538f.png">

<br>

### 보안 그룹의 특징

- 보안 그룹을 여러 인스턴스에 연결 가능 / 한 인스턴스에 여러 보안그룹 연결 가능
    - 보안 그룹과 인스턴스는 일대일 관계가 아니다
- 지역과 VPC 결합으로 통제되어 있다
    - 지역을 전환하면 새 보안그룹 , VPC를 생성해야 한다.
- 보안그룹은 EC2 외부에 있다
- 추천 : SSH 접근을 위한 하나의 분리된 보안 그룹을 유지하는 것이 좋다
- 보안 그룹의 문제 예
    - 타임아웃으로 접근이 불가능
    - 어떤 포트에 연결을 시도하지만 계속해서 대기
- 연결 거부의 문제는 보안그룹이 실행되었고 트래픽은 통과되었으나, 실행되지 않는 등 문제가 생긴 것이다
- 기본적으로 모든 인바운드 트래픽은 차단, 모든 아웃바운드 트래픽은 허용되어 있다

### 알아두어야 할 포트

- 22 = SSH (Secure Shell) - log into a Linux instance
- 21 = FTP (File Transfer Protocol) - upload files into a file share
- 22 = SFTP (Secure File Transfer Protocol) - upload files using SSH
- 80 = HTTP - access unsecured websites
- 443 = HTTPS - access secured websites
- 3389 = RDP (Remote Desktop Protocol) - log into a Window instance

---

## SSH

명령줄 인터페이스 도구

terminal에서 ssh 접근하는 방법

```bash
ssh -i key-file-name.pem ec2-user@public-ip
# Permissions 0644 for 'key-file-name.pem' are too epen

# -> bad permission : 개인키가 유출될 위험이 있다
# 주의 ! 권한을 변경하여 다시 시도할 것
```

하단 명령어를 실행한 후 다시 ssh 접근 시도 

```bash
chmod 0400 key-file-name.pem

# windows : pem 파일 속성 > 보안 > 고급보안설정 > 키 소유자 설정과 사용 권한 변경

```

ssh오 ec2 서버에 접속했으니 이제 명령을 실행할 수 있다

```bash
ssh -i key-file-name.pem ec2-user@public-ip
```

Tip : Windows 10 이상의 버전들에서는 ssh 명령어를 사용할 수 있다

10 이전 버전에서는 `ssh command not found` 문구가 표시되고 putty를 사용해야 한다

<img src="https://user-images.githubusercontent.com/46165696/173852404-af4aa2c2-d573-4b6d-9b06-d97f7528424f.png">

<br>

---

## EC2 가격 정책

오랜 시간 동안 사용해야 하는 서버가 있을 경우 비용 절감이 가능하다

- On-Demand instances : 예측 가능한 가격, 단기간 워크로드 용
`Pay for what you use` → 비싸지만 측정이 가능
- Reserved (최소 1년) 
`온디맨드의 75%까지 절약 가능` → 기간이 길수록, 미리 결제할 수록
    - Reserved Instances : 단순 예약 인스턴스. 장기간 워크로드
    - Convertible Reserved Instances : 전환형 예약 인스턴스. 시간이 지난 후 다른 종류의 인스턴스로 바꿀 수 있는 유연형 인스턴스
    - Scheduled Reserved Instances : 정기 예약 인스턴스. (예시) 매주 목요일 3 ~ 6시 동안만 서버가 필요한 경우 사용
- Spot Instances : 단기간 워크로드. 저렴하나 손실 가능성이 있어서 신뢰도가 낮음
`온디맨드의 90%까지 절약 가능`  → 인스턴스가 언제든 중단될 수 있으니, 실패에 대응할 수 있는 서비스만 사용할 것
- Dedicated Hosts : 전용 호스트. 전체 물리 서버를 예약하고 인스턴스 배치를 제어
    
    `기존 서버 결합 소프트웨어 라이센스의 사용이 가능해서 비용 절감 가능`