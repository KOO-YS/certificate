# Summary

## RBAC
#### ClusterRole
- namespace로 구분이 힘든 리소스들을 그룹화, 격리화
- Role 설정
    1. ClusterRole 객체 생성
    2. ClusterRoleBinding 객체 생성 후 ClusterRole과 나머지 객체들을 연결

#### ServiceAccount
- cluster와 상호작용할 애플리케이션들을 위한 계정