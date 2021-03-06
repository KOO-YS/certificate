# ElastiCache

- 관계형 데이터베이스를 관리할 수 있다
- Redis 나 Memcached 같은 캐시 기술을 관리할 수 있다
- Caches : 높은 성능과 적은 지연시간을 가진 인-메모리 데이터베이스
- 엘라스틱 캐시를 사용하면, 읽기 집약적인 워크로드의 부하(load off)를 줄일 수 있다
- stateless 무상태 어플리케이션이 되도록 돕는다
- RDS와 같이 AWS의 동일한 유지보수
    - 운영 체제, 패치, 최적화, 설정, 구성, 모니터링, 장애 복구, 백업 수행
- *엘리스틱 캐시를 사용하면 어플리케이션 코드를 변경할 필요가 있다*
    - 데이터베이스 쿼리 전과 후에 캐시를 쿼리하도록 애플리케이션을 변경

<br>

### 사례 : ElastiCache Solution Architecture - DB Cache

애플리케이션, RDS, 엘라스틱 캐시가 있는 경우

- 애플리케이션은 엘라스틱 캐시를 쿼리
    - **cache hit** : 쿼리가 기존에 생성되어 있는지, 엘라스틱 캐시에 저장되어 있는지 확인하는 것. 엘라스틱 캐시에서 바로 응답을 얻고, **RDS로 이동하는 동선을 줄여줌**
    - **cache miss** : 데이터베이스에서 데이터를 가져와서 데이터를 읽는 것. 동일한 쿼리가 발생하는 다른 인스턴스를 위해 **데이터를 캐시에 다시 기록** → cache hit를 이용할 수 있도록 함

    
- RDS의 부하를 줄이는데 도움
- 캐시 무효화 전략이 있어야 하며 가장 최근 데이터만 사용하는지를 확인해야 할 필요

<br>

### **사례 : ElastiCache Solution Architecture - User Session Store**

사용자 세션을 저장해 애플리케이션을 무상태로 만드는 것

- 사용자가 애플리케이션의 계정에 로그인 하면 엘라스틱 캐시에 세션 데이터를 기록
- 사용자가 애플리케이션의 다른 인스턴스로 리다이렉션 되면 애플리케이션은 엘라스틱 캐시에서 세션 캐시를 검색
    - 사용자는 계속 로그인한 상태로 한 번 더 로그인 할 필요가 없음


<br>

### Redis vs Memcached : 차이

**Redis** 

- 자동 장애 조치(Auto Failover)로 다중 AZ를 수행하는 기술
- 읽기 전용 복제본으로 읽기 스케일링에 사용되고 고가용성
- AOF persistence(지속성)으로 인한 데이터 내구성
- 백업과 기능 복원 기능


<br>


**Memcached**

- 데이터 분할(partitioning of data)에 다중 노드 사용 → **shading**
- 가용성이 높지 않고 복제가 발생하지 않는다
- 지속적인 캐시가 아니다
- 백업, 복원 없음
- 다중 스레드 아키텍처이며 여러 샤딩으로 캐시와 함꼐 실행된다

---

<br>

**결론 (차이)** 

redis : 고가용성 백업, 읽기 전용 복제본

memcached : 데이터를 손실할 수 없는 단순한 분산 캐시

<br>

### Cache Security

- 엘라스틱 캐시의 모든 캐시는 IAM 인증을 지원하지 않음
    - AWS API 수준 보안에만 사용 ( → 캐시 생성, 캐시 삭제 같은 종류의 작업)
- 레디스 AUTH
    - 레디스를 인증하기 위해 사용
    - 레디스 클러스터를 생성할 때 비밀번호/토큰을 설정 가능
    - 캐시에 대한 추가적인 레벨의 보안
    - 전송중 암호화(flight encryption)를 위해 SSL 보안 지원
- Memcached
    - 좀 더 높은 수준의 SASL 기반 인증 지원

<br>

## ElasctiCache 패턴

엘라스틱 캐시에 데이터를 불러오는 패턴

- Lazy Loading : 모든 읽기 데이터가 캐시되고, 데이터는 캐시에서 불안정할 가능성
- Write Through : 데이터가 기록될 때마다 새로 추가하거나 업데이트
- Session Store : 일시적인 세션 데이터를 캐시에 저장. TTL(time to live) 속성으로 세션 만료 시킬 수 있다

<br>

### Lazy Loading

- 엘라스틱 캐시에 캐시가 히트하면 애플리케이션이 캐시로부터 데이터를 받는다
- 캐시 미스가 있을 경우 데이터베이스에서 데이터를 읽고 캐시에 쓴다
- 캐시 히트가 없을 때만 발생하기 때문에, Lazy Loading이라고 지칭
- **redis 사용 사례 : 게임 리더보드 생성**
    - 고유성과 요소 순서 모두 보장하는 정렬된 집합 을 가지고 실시간으로 1, 2, 3위를 가려내는 개념 → 영상 참고
