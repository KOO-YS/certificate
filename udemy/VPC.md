## CIDR : Classless Inter-Domain Routing

- 클래스 없는 도메인 간 라우팅
- IP 주소를 할당하는 방법
- 보안 그룹 규칙과 AW 네트워킹에 사용
    - 보안 그룹 규칙의 Source의 `*.*.*.*/*` → IP 범위 정의
- CIDR 구성요소
    - IP 구성요소 중에서 Base IP 범위 → 범위의 시작점
    - 서브넷 마스크 → IP에서 변경 가능한 비트의 개수 정의
### IPv4 환경의 Public IP vs Private IP

- IANA (The Internet Assigned Numbers Authority)이 구축한 특정 IPv4 주소 블록은 private 사설(LAN)과 공용(Internet) 주소로 사용된다
- Private IP : 특정 값만 허용
- Public IP : 이외의 전 세계에 있는 IP 주소. 인터넷상에 있는 공용 IP 주소

<br>

# VPC : Virtual Private Cloud
- 단일 AWS 리전에 여러 VPC를 둘 수 있으며, 리전당 최대 5개까지 가능 (원하면 늘릴 수 있음 - soft limit)
- VPC는 사설 리소스이기 때문에 사설 IPv4범위만 허용
- VPC CIDR은 다른 VPC나 네트워크, 기업 네트워크와 절대 겹치면 안된다

---

- 새로운 AWS 계정은 모두 기본 VPC가 있으며 바로 사용 가능하다
- 새로운 EC2 인스턴스는 서브셋을 지정하지 않으면 기본 VPC에 실행된다
    - 기본 VPC는 처음부터 인터넷에 연결되어 있어서 인스턴스가 인터넷에 액세스하고 또 내부의 EC2 인스턴스는 공용 IPv4 주소를 얻는다
    - EC2 인스턴스를 생성하자마자 연결할 수 있는 이유
- EC2 인스턴스를 위한 공용 및 사설 IPv4 DNS 이름을 얻는다

<br>


## Subnet

- VPC 내부에 있는 IPv4 주소의 부분 범위
- 이 범위 내에서 AWS가 IP 주소 다섯 개를 예약
    - 처음 4개와 마지막 1개를 서브넷마다 예약 → 이 주소들은 IP 주소로 사용될 수 없으며 EC2 인스턴스에 IP로 할당되지 못함
- 예약된 5개의 IP 주소 → [example] `10.0.0.0/24`
    - `10.0.0.0` : 네트워크 주소
    - `10.0.0.1` : `.1` → VPC 라우터용으로 예약
    - `10.0.0.2` : `.2` → AWS가 제공한 DNS에 매핑
    - `10.0.0.3` : `.3` → 미래 사용을 위해 예약
    - `10.0.0.255` : `.255` → 네트워크 브로드캐스트 주소. ⚠️ AWS는 VPC에서 브로드캐스트를 지원하지 않기에 예약은 되었지만 사용은 불가능

<br>


### IGW : Internet Gateway

- 인터넷 게이트웨이 : VPC 내부 (EC2 인스턴스나 람다 함수 등) 모든 리소스를 인터넷에 연결
- 수평으로 확장되고 가용성과 중복성이 높은 관리형 리소스
- VPC와는 별개로 생성해야 함
- VPC는 인터넷 게이트웨이와 1:1로 연결
- 인터넷 게이트웨이 자체는 인터넷 액세스를 허용하지 않음
    - VPC와 연결 후 라우팅 테이블 역시 수정이 필요
    - 라우팅 테이블을 생성 후 VPC를 할당, Subnet Associations에서 서브넷을 추가


<br>

### Bastion Host

- 인스턴스를 사설 서브넷에 연결하는 방법
- 공용 서브넷의 EC2 인스턴스로 통과할 수 있는 호스트
- hop : Bastion 호스트를 통해 사설 서브넷으로 홉
    1. 사설 서브넷 EC2 인스턴스의 보안 그룹 규칙을 수정
    2. 사용자가 직접 공용 서브넷 Bastion 호스트인 EC2 인스턴스에 SSH 하도록 허용
    3. Bastion 호스트에서 사설 서브넷의 EC2 인스턴스로 SSH 

<br>

### NAT Instance (Deprecated)

- NAT : Network Address Translation 네트워크 주소 변환
- 사설 서브넷의 EC2 인스턴스가 인터넷에 연결되도록 허용
- NAT 인스턴스는 반드시 퍼블릭 서브넷에서 실행되어야 함
- 특정 설정 비활성화 → 소스/목적지 확인
- NAT 인스턴스는 고정된 탄력적 IP가 연결되어야 함
- NAT Instance의 단점
    - 가용성이 높지 않고 초기화 설정으로 복원할 수 없기 떄문에 여러 AZ에 ASG를 생성해야 하고, 복원되는 사용자 데이터 스크립트가 필요하는 등 복잡
    - 대역폭이 작음
    - 보안 그룹과 규칙을 관리해야 함
        - Inbound : 사설 서브넷의 HTTP, HTTPS
        - outbound : 인터넷으로부터  HTTP, HTTPS
        
<br>

## NAT Gateway

- AWS의 관리형 NAT 인스턴스이며 높은 대역폭을 가짐
- 가용성이 높으며 관리할 필요가 없음, 사용량과 대역폭에 따른 비용 청구
- 특정 AZ에서 생성되고 탄력적 IP를 이어받음
    - EC2 인스턴스와 같은 서브넷에서 사용할 수 없어서 다른 서브넷에 엑세스할 때만 NAT Gateway가 도움
- 공용 서브넷에서 NAT Gateway를 만들고 사설 서브넷의 인스턴스와 연결
- 경로가 사설 서브넷 → NAT Gateway → Internet Gateway
- 대역폭은 초당 5GB이며 자동으로 초당 45GB까지 확장 가능
- 보안 그룹 관리할 필요 없음
- **고가용성 NAT Gateway**
    - 단일 AZ에서 복원 가능
    - 단일 AZ 내에서만 중복되지만 AZ가 중지될 경우를 위해 여러 AZ에 다중 NAT Gateway를 두어 결함 감내(결함 허용)