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