## Keyword Note
> From dump study

AWS CloudFront
- `.html`, `.css`, `.php`, `이미지`, and `미디어 파일` 같이 웹 콘텐츠 전송속도를 향상시킬 수 있는 서비스
- 콘텐츠 전송 네트워크(CDN) 서비스
- 전 세계 곳곳에 Edge Location(cache)를 두고 사용자에게 가장 가까운 곳을 찾아 latency를 최소화

---

AWS CloudFormation
- AWS 리소스를 모델링 설정 -> 리소스 관리 시간을 줄여준다
- 리소스 템플릿을 생성하면 해당 리소스의 프로비저닝과 구성을 담당하며 모든 것을 처리
- json, yaml로 작성
- AWS Serverless Application Model(SAM)을 활용하여 서버리스 애플리케이션을 더 빠른 속도로 구축
  - AWS Serverless Application Model(SAM) : 간편 구문을 통해 함수, API, 데이터베이스 및 이벤트 원본 매핑을 표현하는 오픈소스 프레임워크
\* [추가설명](https://nearhome.tistory.com/117)

---

Direct Connect (DX)
- 온프레미스에서 AWS로 전용 네트워크 연결을 쉽게 설정할 수 잇는 서비스
\* [추가설명](https://dev.classmethod.jp/articles/what-is-the-aws-dx-kr/)

---

DynamoDB Accelerator (DAX)
- DynamoDB 가속기
- DynamoDB용 완전관리형 고가용성 인메모리 캐시
- 초당 수백만 개의 요청에도 밀리~마이크로초까지 최대 10배의 성능
- 대규모 데이터를 많이 **읽는** 애플리케이션에 유용
- DynamoDB와 마찬가지로 프로비저닝한 용량에 대해서만 비용을 지불
  - 캐시된 데이터를 검색하면 기존 DynamoDB 테이블에서 읽기 로드가 줄어들기 때문에 프로비저닝된 읽기 용량을 줄이고 전체 운영 비용을 낮출 수 있다


--- 

OAI (Origin Access IDentity)
- S3 엔드포인트로 접근 시, 엔드포인트 주소는 버킷명을 포함하고 있기 때문에 누군가 직접 접근할 위험성이 있다. -> OAI를 활용해 CloudFront 배포로만 웹사이트에 접근할 수 있도록 설정
  - S3만을 위한 서비스 : S3 Origin 보안 강화를 위해 요청을 식별하는데 사용
- CloudFront에서 Lambda 함수를 사용 -> [Lambda@Edge](https://docs.aws.amazon.com/ko_kr/AmazonCloudFront/latest/DeveloperGuide/lambda-examples.html)

---

DMS (Database Migration Service)
- AWS에서 제공하는 RDB 마이그레이션 서비스
  - 관계형 데이터베이스, 데이터 웨어하우스, NoSQL 데이터베이스 및 기타 유형의 데이터 저장소를 쉽게 마이그레이션할 수 있는 서비스
- AWS Schema Conversion Tool 변환을 완료하는데 필요한 변환 보고서를 생성
  - `ex` Oracle에서 Aurora MySQL로 변환 보고서를 실행하고, DMS를 사용하여 데이터를 마이그레이션
  
--- 

Enhanced Monitoring (향상된 모니터링)
- DB 인스턴스, 클러스터가 실행되는 운영 체제에 대한 측정치를 실시간으로 제공
- 다른 프로세스 또는 스레드에서 CPU 사용방법을 확인하려면 이 지표가 유용
- RDS는 지표를 확장 모니터링에서 CloudWatch Logs 계정으로 전달, 지표 필터를 생성 후 대시보드에 그래프로 표현


--- 

Service Catalog
- AWS 리소스들을 중앙에서 관리할 수 있고, 규정 준수 요건을 충족할 수 있다
- 최종 사용자는 조직에서 규정한 제약, 필요에 따라 승인된 리소스만 신속하게 배포 가능
- 세분화된 액세스 제어
  - 카탈로그 관리자 : 애플리케이션과 서비스의 카탈로그 관리. 최종 사용자에게 액세스 권한 부여. CloudFormation 템플릿, 제약조건을 구성하고 IAM 역할을 관리
  - 최종 사용자 : 액세스 권한을 받은 제품을 시작. 

---

Kinesis 
- 실시간 비디오 및 데이터 스트림을 손쉽게 수집, 처리 및 분석하기 위한 서비스
- Kinesis Data Streams : 데이터 스트림으로 스트리밍 **데이터 수집**
- Kinesis Data Firehose : 데이터 전송 스트림. 스트리밍 **데이터를 처리 및 전송**
- Kinesis Data Analytics : 데이터 분석 애플리케이션을 사용하여 **스트리밍 데이터를 분석**

---

Memcached 
- 범용 분산 캐시 시스템. 고성능 인메모리 데이터 스토어

---

OU (organizational units)
- 조직 단위 관리
- 계정을 그룹으로 만들어 단일 유닛으로 관리할 수 있다
- 단일 조직에서 여러 OU를 만들 수 있으며, OU 안에 OU를 생성할 수도 있다
- (ex) 정책 기반 제어를 OU에 연결하면, OU 안의 모든 계정이 정책을 자동으로 상속
- SCP (Service Control Policy)를 만들어서 OU에 정책을 연결할 수 있다

---

Global Accelerator
- 글로벌 네트워크 인프라를 사용하여 사용자 트래픽의 성능을 최대 60%까지 개선하는 네트워킹 서비스
- 애플리케이션에 대한 고정된 진입점 역할을 하는 글로벌 정적 퍼블릭 IP 2개를 제공하여 가용성 개선
- 가장 가까운 위치의 사용 가능한 정상 엔드포인트로 트래픽을 자동으로 재라우팅하여 엔드포인트 장애를 완화

---

System Manager
- 여러 AWS 서비스의 운영 데이터를 중앙집중화하고 AWS 리소스 전체에서 작업을 자동화할 수 있다
- 애플리케이션, 애플리케이션 스택의 계층, 프로덕션 환경 대 개발환경 등의 논리적 리소스 그룹 생성 가능
- 리소스 그룹을 선택하여 리소스 그룹의 최근 API 작업, 리소스 구성 변경, 관련 알림, 운영 경보, 소프트웨어 인벤토리, 패치 규정 준수 상태를 볼 수 있다
- System Manager Explorer : AWS 환경의 운영 상태 및 성능에 대한 핵심 통찰력과 분석 결과를 제공하는 사용자 지정 가능 대시보드