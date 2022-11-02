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