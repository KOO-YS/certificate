# AWS RDS

- SQL을 쿼리 언어로 사용하는 관계형 데이터 베이스 서비스
- AWS에서 관리되는 클라우드 데이터베이스
    - Postgres
    - MySQL
    - MariaDB
    - Oracle
    - Microsoft SQL Server
    - Aurora (AWS proprietary database : AWS가 소유권을 가진 DB)


- **EC2 인스턴스 상에 자체 데이터베이스를 배포하지 않고 RDS를 사용하는 이유는?**
    - 데이터베이스 뿐만 아니라 여러 기타 서비스도 제공하기 때문
        - 데이터베이스 프로비저닝 자동화, OS 패치
        - 지속적인 백업, 특정 타임스탬프로 복구 → 지정시간 복구 (PITR : Point In Time Restore)
        - 모니터링 대시보드
        - 읽기 성능 향상을 목적으로 하는 리플리카
        - 다중 AZ 설정 (재해 복구)
        - 업그레이드를 위한 유지보수
        - EBS를 기반 스토리지
    - RDS 인스턴스는 SSH 접근이 불가

<br>

**RDS 백업**

- 백업 : RDS에서 자동으로  활성화 & 자동 생성
- 정의해 놓은 유지관리 기간 동안 매일 전체 백업
- 매 5분마다 트랜잭션 로그가 RDS에 백업