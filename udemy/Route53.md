# Route 53

## DNS 란?

- Domain Name System
- 호스트 이름, url 을 대상 서버 **IP 주소로 번역**
- 계층적 이름 구조를 가지고 있다

### DNS Terminologies : 관련 용어

- Domain Registrar : 도메인 이름을 등록하는 곳 (→ route 53)
- DNS Recoreds :
- Zone file : DNS 레코드를 포함. 호스트 이름과 IP, 주소를 일치시키는 방법
- Name Server : DNS 쿼리를 실제로 해결하는 서버
- Top Level Domain(TLD) : com, us, in, gov, org
- Second Level Domain(SLD) : amazon.com, google.com
- Root (마지막 점) → TLD → SLD → Sub Domain → Domain Name → Protocol

### DNS 동작 방법

- 공인 IP 가 하나 있을 때, 도메인 이름으로 접근을 시도
    - 원하는 도메인 이름을 가지고 DNS 서버를 등록해야 한다
1. 사용자가 웹 브라우저에 도메인 이름을 가지고 접근을 시도
2. Local DNS Server에 도메인 이름을 검색
3. Local Server가 이 쿼리를 본적이 없다면, **(ICANN에 의해 관리되는 )DNS 서버의 루트**에 검색
    1. 루트 DNS 서버는 TLD부터 검색 → 만약 (예시)com을 안다면 해당 이름 서버 레코드를 알려줌
    2. **(IANA에 의해 관리되는 )TLD DNS Server**(예 : com 도메인 서버)에게 쿼리의 답을 요청
4. 해당 서버를 알고 있다면 ip를 전달
5. 최종, 서브 도메인(SLD) DNS 서버는 해당 주소 항목을 결과로 보냄
6. 차례로 물어본 결과로 웹 서버를 얻을 수 있고, 앞으로 다시 같은 주소를 물어본다면 바로 답변 가능

<br>

# Amazon Route 53

- 고가용성, 확장성을 가진, 완전히 관리되며 권한있는 DNS
    - 권한이 있다 →사용자가 DNS 레코드를 업데이트 할 수 있다
- 도메인 레지스트라 → 도메인 이름을 등록 가능
- 리소스 관련 상태 확인 가능
- 100% SLA 가용성을 제공하는 유일한 AWS 서비스
- 53란 ? DNS 서비스, 이름에서 사용되는 전통적인 DNS 포트

<br>

### Route 53 - Records

- Route 53에서 여러 DNS 레코드를 정의하고 레코드를 통해 특정 도메인으로 라우팅하는 방법 정의
- 각 레코드는 도메인, 서브 도메인 이름 정보를 포함
- **레코드 종류 : A, AAAA, CNAME, NS**
- 레코드의 값 : 123.456.789.123
- 라우팅 정책 : route 53이 쿼리에 응답하는 방식
- TTL : DNS가 resolver에서 레코드가 캐싱되는 시간

<br>


### Route 53 - Record Types

- A : IPv4와 호스트 이름을 매핑
- AAAA : IPv6와 호스트 이름을 매핑
- CNAME : 호스트 이름을 다른 호스트 이름과 매핑
    - 대상 호스트 이름은 A, AAAA에 포함된 호스트 이름일지도..
    - DNS 이름공간 또는 Zone Apex의 상위 노드에 대한 CNAMES를 생성할 수 없다
- NS : 호스팅 존의 이름 서버, 서버의 DNS 이름 또는 IP 주소로 호스팅 존에 대한 DNS 쿼리에 응답 가능. 트래픽이 도메인으로 라우팅 되는 방식을 제어

<br>


### Route 53 - Hosted Zones

- 레코드의 컨테이너
- 도메인과 서브도메인으로 가는 트래픽의 라우팅 방식 정의
- 호스팅 존
    - 퍼블릭 호스팅 존
        - 인터넷에서 라우팅이 트래픽되는 법을 구분하는 레코드를 포함
        - 이름을 살때마다 퍼블릭 호스팅 존을 만들 수 있다
        - 쿼리에 도메인 이름의 IP가 무엇인지 알 수 있다
    - 프라이빗 호스팅 존
        - VPC 내부에서만 라우팅이 트래픽되는지를 구분하는 레코드 포함
        - 공개되지 않는 도메인 이름을 지원
        - 가상 프라이빗 클라우드 (VPC) 만이 url을 리졸브 할 수 있다
    - 각 호스트 존마다 월 $0.50 을 지불해야 한다