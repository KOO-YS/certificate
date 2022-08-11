# CloudWatch

- AWS 모든 서비스에 대한 지표(metrics)를 제공
- 지표는 namespaces에 속함
- 배열(Dimension) : 지표의 속성 (인스턴스 아이디, 환경 등) → 지표 당 최대 10개의 배열
- 지표는 timestamps 를 가지고 있으며, 클라우드와치 대시보드를 만들 수 있다

<br>

### EC2 Detailed Monitoring

- 기본적으로 EC2 인스턴스는 5분마다 지표를 가진다
    - 추가 비용으로 세부 모니터링 활성화 → 1분 간격 가능
    - 사용자 지정 지표가 푸시 되어야만, EC2 Memory usage (RAM)이 푸시된다

<br>


### CloudWatch Custom Metrics

- CloudWatch에서 직접 지표를 정의해 사용자 지정 지표를 얻을 수 있다
    - metric.json 파일로 지표 푸시 가능
- RAM 메모리 사용량이나 디스크 공간, 애플리케이션 로그인 사용자 수 등 CloudWatch에 푸시하고 싶다면 `put-metric-data` 라는 API 호출
- 2주 전, 2시간 후 언제 지표를 푸시한다고 해도 오류가 나지 않는다 → EC2 인스턴스의 시간을 정확히 할 것
- 타임스탬프를 지정한 후 → 데이터를 지정, 지표의 이름과 단위값, 배열, 스토리지 해상도까지 지정 가능

<br>


# CloudWatch Dashboards

- 주요 지표와 경보에 접근 가능
- 글로벌화. **다른 AWS 계정이나 다른 리전의 그래프도 포함 가능**
- 대시보드에서 시간대와 시간 범위를 바꿀 수 있고 자동 새로고침 설정 가능
- AWS 계정이 없는 사람들과 대시보드 공유 가능

<br>


# CloudWatch Logs

- 로그들을 로그 그룹으로 그룹화
    - 로그 그룹의 이름 → 주로 애플리케이션을 나타냄
- 로그 스트림 : 애플리케이션 내부 인스턴스, 로그 파일명, 컨테이너 등을 나타냄
- 로그 만료 정책을 수립할 수 있다 (영구 또는 일정 기간 후 만료)
- 유료 서비스
- 로그를 보내는 곳
    - Amazon S3 (exports)
    - Kinesis Data Streams
    - Kinesis Data Firehose
    - Lambda
    - ElasticSearch
- 로그의 유형
    - SDK, CloudWatch Logs Agentm CloudWatch 통합 에이전트
    - Elastic Beanstalk : 애플리케이션의 로그 콜렉션을 CloudWatch에 전송
    - ECS : 컨테이너의 로그를 전송
    - Lambda : 함수 자체에서 로그를 전송
    - VPC Flow Logs : VPC 메타 데이터 네트워크 트래픽 로그를 전송
    - API Gateway : 받은 모든 요청을 전송
    - CloudTrail : 필터링해서 로그 전송 가능
    - Route 53 : 모든 DNS 쿼리를 로그로 저장

<br>


### Logs Metrics Filter & Insights

- 필터 표현식 사용 가능 → 예 : 로그 내 특정 IP 확인, 로그 레벨 검색
- 지표 필터를 통해 출현 빈도를 계산하여 지표 생성 후 경보로 연동 가능
- Logs Insights는 로그를 쿼리하고 이 쿼리를 대시보드에 바로 추가할 수 있다

<br>


### S3 Export

- CloudWatch 에서 S3로 보낼 때 내보내기가 가능해질 때까지 최대 12시간이 걸릴 수 있다
- API : `CreateExportTask` (실시간 성이 아님)
- CloudWatch Logs에서 로그를 스트림하고 싶다면 구독 필터(로그를 목적지로 보내는 필터) 사용