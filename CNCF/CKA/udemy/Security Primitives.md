# Security Primitives

- 당연하게 호스트로 가는 모든 접근은 안전해야 한다
    - 라우팅된 접근 X, 패스워드 기반 인증 X
    - SSH key 기반 인증만 가능 O

### cluster의 보안을 염려하게 만드는 문제들
- kuber-apiserver : 쿠버네티스의 모든 작업들의 중심
    = kubectl utility와 통신하거나 API에 직접 접근한다

#### Who can access
- 인증 메커니즘에 의해 정해진다
- 인증 메커니즘 종류
    - ID, Password
    - Token
    - Certificates
    - External Authentication Providers - LDAP
    - **service account**

#### What can they do?