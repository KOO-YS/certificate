# IAM

### Identity and Access Management, Global Service

- root 계정은 기본으로 생성되지만, 사용하지도 공유하지도 말아야 한다 → 대신해서 user를 생성할 것
- user를 그룹화 할 수 있지만, 그룹 내 그룹은 있을 수 없다.
- user는 어떤 그룹에 속하지 않을 수도, 여러 그룹에 속할 수도 있다.

## Permission

사용자와 그룹을 생성하는 이유가 무엇일까? → AWS 계정을 사용하도록 허용하고, 허용을 위해 사용자에게 권한을 주는 것

### IAM Document

사용자와 그룹 권한 정보를 Json 형식으로 정의할 수 있다.

AWS는 사용자에게 모든 권한을 주지 않는다. 새로운 사용자가 비용이나 보안 문제를 야기할 수 있기 때문

**AWS에서는 “최소 권한의 원칙 (least privilege principle)”을 적용한다**

사용자가 꼭 필요로 하는 것 이상의 권한을 주지 않는다는 뜻