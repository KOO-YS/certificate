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

**사례 : ElastiCache Solution Architecture - DB Cache**

애플리케이션, RDS, 엘라스틱 캐시가 있는 경우

- 애플리케이션은 엘라스틱 캐시를 쿼리
    - **cache hit** : 쿼리가 기존에 생성되어 있는지, 엘라스틱 캐시에 저장되어 있는지 확인하는 것. 엘라스틱 캐시에서 바로 응답을 얻고, **RDS로 이동하는 동선을 줄여줌**
    - **cache miss** : 데이터베이스에서 데이터를 가져와서 데이터를 읽는 것. 동일한 쿼리가 발생하는 다른 인스턴스를 위해 **데이터를 캐시에 다시 기록** → cache hit를 이용할 수 있도록 함
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/da878dc3-dd44-4007-8c9a-e089ae218ff5/Untitled.png)
    
- RDS의 부하를 줄이는데 도움
- 캐시 무효화 전략이 있어야 하며 가장 최근 데이터만 사용하는지를 확인해야 할 필요

<br>

**사례 : ElastiCache Solution Architecture - User Session Store**