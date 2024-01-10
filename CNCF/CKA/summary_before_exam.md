# Summary

## RBAC
**ClusterRole**
- namespace로 구분이 힘든 리소스들을 그룹화, 격리화
    - ex) node, pv, role, role binding, certificate
- Role 설정
    1. ClusterRole 객체 생성
    2. ClusterRoleBinding 객체 생성 후 ClusterRole과 나머지 객체들을 연결

**ServiceAccount**
- cluster와 상호작용할 애플리케이션들을 위한 계정

```shell
# cluster role 생성 (test-namespace 내부 Deployment, Daemonset 리소스를 대상으로 create만 허용)
k create clusterrole c-role --verb=create --resource=Deployment,Daemonset -n test-namespace

# service account 생성
k create sa s-account -n test-namespace

# clusterrole와 serviceaccount를 연동
k create clusterrolebinding c-role-binding --clusterrole=c-role --serviceaccount=test-namespace:s-account
```

#### plus

```shell
# Deployment 리소스에 대해 create를 수행할 접근 권한 확인
k auth can-i create Deployment --as=system:serviceaccount:test-namespace:s-account
# output : yes or no
```


## Schedule
- 업그레이드, 패치 적용들의 작업

**drain**
- 해당 노드를 스케줄러에서 제외시켜 pod가 할당되지 않도록 하며, 기존 운영되고 있던 pod 역시 다른 노드로 이동시킨다
- Daemonset로 인해 오류가 발생하는 경우 `--ignore-daemonsets` 추가

**cordon**
- 기존에 운영중인 pod는 남겨두고, 새로운 pod가 스케줄링되는 것만 제지

**uncordon**
- 정상적으로 재기동된 node를 정상적으로 스케줄링에 포함
- 옆 node로 이동한 pod는 원복되지 않는다
- 기존 pod가 삭제되거나 새로운 pod가 생성되지 않는 한 node는 비어있다 

```shell
k cordon node01

k drain node01 --ignore-daemonsets

k get nodes -l tier=bronze

# NAME      STATUS                      ROLES
# master    Ready                       control-plane, master
# node01    Ready,SchedulingDisabled    <none>
```

### Upgrade

```shell
k cordon master
k drain master

ssh master
sudo -i


```


## ETCD
- key:value 저장소
- etcdctl : ETCD와 통신하기 위해 사용되는 CLI 툴

```shell
# set ectd api version
export ETCDCTL_API=3

# etcdctl이 etcd api service를 인증할 수 있도록 certificate 패스 지정
sudo -i

# 8080 포트에서 돌아가는 etcd 서버를 /var/lib/backup/etcd-snapshot.db에 스냅샷 저장
ETCDCTL_API=3 --endpoints=https://127.0.0.1:8080 \
	--cacert=/opt/KUIN00601/ca.crt \
	--cert=/opt/KUIN00601/etcd-client.crt \
	--key=/opt/KUIN00601/etcd-client.key \
    snapshot save /var/lib/backup/etcd-snapshot.db

# 기존 /var/lib/backup/etcd-snapshot.db의 스냅샷을 이용해 복구
ETCDCTL_API=3 --endpoints=https://127.0.0.1:8080 \
	--cacert=/opt/KUIN00601/ca.crt \
	--cert=/opt/KUIN00601/etcd-client.crt \
	--key=/opt/KUIN00601/etcd-client.key \
    snapshot restore /var/lib/backup/etcd-snapshot.db

exit
```


## NetworkPolicy
- 1개 이상의 pod에 트래픽 정책을 지정
    - 지정된 ingress 트래픽만 허용

myproject 라벨을 가진 리소스에 6379 포트 ingress 트래픽 정책 지정
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              project: myproject
      ports:
        - protocol: TCP
          port: 6379
```

```shell
k get ns --show-label
# 정책 지정 전 라벨 설정
k label ns myproject app=myproject
```

## Service

**Deployment 수정**

```shell
# 기존에 있는 Deployment 수정
k edit deploy deploy01
```

```yaml
apiVersion: v1
kind: Deployment
metadata:
  name: deploy01
  labels:
    app: dp
spec:
  containers:
  - name: nginx
    image: nginx:stable
    # 포트 정의
    ports:
      - containerPort: 80
        name: http # service 리소스의 name
        protocol: TCP
```

**Deployment의 Service 포트 지정**
```shell
k expose deployment deploy01 --name=dp-service --type=NodePort --port=80 --dry-run=client -o yaml > dp-service.yaml

vi dp-service.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: dp-service
spec:
  selector:
    app: dp # 라벨로 구분
  type: NodePort
  ports:
    - port: 80
      # By default and for convenience, the `targetPort` is set to
      # the same value as the `port` field.
      targetPort: 80
      # Optional field
      # By default and for convenience, the Kubernetes control plane
      # will allocate a port from a range (default: 30000-32767)
      nodePort: 30007
      name: http # service 포트 이름 설정
      protocol: TCP
```


## Ingress
- 같은 클러스터 안에서 분리된 deployment를 배포할 수 있다

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingre # 이름 지정
  namespace: test-ns # 네임스페이스 이름 지정
spec:
  ingressClassName: nginx-example
  rules:
  - http: # ingress 트래픽 지정
      paths:
      - path: /test
        pathType: Prefix
        backend:
          service:
            name: test-svc # 서비스 이름 지정
            port:
              number: 5678
```

```shell
k create ingress ingre -n test-ns --class=default --rule="/test=test-svc:5678"
```

## Scale

```shell
k scale deployment dp01 --replicas=3
```


## NodeSelector

```shell
k label nodes <your-node-name> disktype=ssd

k get nodes -l disktype=ssd
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd # 라벨이 일치하는 노드에서 운영
```

## Taint
```shell
k describe node | grep -i taint
# taint output이 <none>이라면 스케줄링 가능
# taint -> node-role.kubernetes.io/master:NoSchedule -> 스케줄링 불가능
```