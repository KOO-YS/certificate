# Practice Command

### Service

service 

```jsx
k create service clusterip redis-service --tcp=6379
```

- help
    
    ```bash
    controlplane ~ ✖ k create service --help
    Create a service using a specified subcommand.
    
    Aliases:
    service, svc
    
    Available Commands:
      clusterip      Create a ClusterIP service
      externalname   Create an ExternalName service
      loadbalancer   Create a LoadBalancer service
      nodeport       Create a NodePort service
    
    Usage:
      kubectl create service [flags] [options]
    ```
    
    ```bash
    controlplane ~ ➜  k create service clusterip --help
    Create a ClusterIP service with the specified name.
    
    Examples:
      # Create a new ClusterIP service named my-cs
      kubectl create service clusterip my-cs --tcp=5678:8080
      
      # Create a new ClusterIP service named my-cs (in headless mode)
      kubectl create service clusterip my-cs --clusterip="None"
    
    Options:
        --allow-missing-template-keys=true:
            If true, ignore any errors in templates when a field or map key is missing in the
            template. Only applies to golang and jsonpath output formats.
    
        --clusterip='':
            Assign your own ClusterIP or set to 'None' for a 'headless' service (no loadbalancing).
    
        --dry-run='none':
            Must be "none", "server", or "client". If client strategy, only print the object that
            would be sent, without sending it. If server strategy, submit server-side request without
            persisting the resource.
    
        --field-manager='kubectl-create':
            Name of the manager used to track field ownership.
    
        -o, --output='':
            Output format. One of: (json, yaml, name, go-template, go-template-file, template,
            templatefile, jsonpath, jsonpath-as-json, jsonpath-file).
    
        --save-config=false:
            If true, the configuration of current object will be saved in its annotation. Otherwise,
            the annotation will be unchanged. This flag is useful when you want to perform kubectl
            apply on this object in the future.
    
        --show-managed-fields=false:
            If true, keep the managedFields when printing objects in JSON or YAML format.
    
        --tcp=[]:
            Port pairs can be specified as '<port>:<targetPort>'.
    
        --template='':
            Template string or path to template file to use when -o=go-template, -o=go-template-file.
            The template format is golang templates
            [http://golang.org/pkg/text/template/#pkg-overview].
    
        --validate='strict':
            Must be one of: strict (or true), warn, ignore (or false).              "true" or "strict" will use a
            schema to validate the input and fail the request if invalid. It will perform server side
            validation if ServerSideFieldValidation is enabled on the api-server, but will fall back
            to less reliable client-side validation if not.                 "warn" will warn about unknown or
            duplicate fields without blocking the request if server-side field validation is enabled
            on the API server, and behave as "ignore" otherwise.            "false" or "ignore" will not
            perform any schema validation, silently dropping any unknown or duplicate fields.
    
    Usage:
      kubectl create service clusterip NAME [--tcp=<port>:<targetPort>] [--dry-run=server|client|none]
    ```
    

Create a pod called `httpd` using the image `httpd:alpine` in the default namespace. Next, create a service of type `ClusterIP` by the same name `(httpd)`. The target port for the service should be `80`.

```bash
k run httpd --image=httpd:alpine --port=80 --expose
```

Create a service `messaging-service` to expose the `messaging` application within the cluster on port `6379`.

```bash
 kubectl expose pod messaging --port=6379 --name messaging-service
```

### Scheduling

Manually schedule the pod on `node01`.

```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01 # ADD !
  containers:
  -  image: nginx
     name: nginx
```

### Selector

How many objects are in the `prod` environment including PODs, ReplicaSets and any other objects?

```bash
kubectl get all --selector env=prod --no-headers | wc -l
```

### Taint

Create a taint on `node01` with key of `spray`, value of `mortein` and effect of `NoSchedule`

```bash
k taint nodes node01 spray=mortein:NoSchedule
```

Create another pod named `bee` with the `nginx` image, which has a toleration set to the taint `mortein`.

```bash
---
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
  tolerations:
  - key: spray
    value: mortein
    effect: NoSchedule
    operator: Equal
```

Remove the taint on `controlplane`, which currently has the taint effect of `NoSchedule`.

```bash
controlplane ~ ➜  k taint nodes controlplane node-role.kubernetes.io/control-plane:NoSchedule-
node/controlplane untainted
```

- `taint-effect` 속성은 taint에 저항하지 못하는 pod가 취해야하는 조치 방법을 제시한다
    - NoSchedule : 해당 노드에 위치할 수 없다
    - PreferNoSchedule : 해당 노드에 파드를 위치시키기를 기피하지만, 확실하지 않다
    - NoExecute : 새 파드가 해당 노드에 위치시키지 않고, 기존에 있던 노드 역시 toleration이 발견되지 않는다면 퇴거 시킨다
```bash
kubectl describe node controlplane | grep -i taints
```

### Node Affinity

Set Node Affinity to the deployment to place the pods on `node01` only.

- Name: blue
- Replicas: 3
- Image: nginx
- NodeAffinity: requiredDuringSchedulingIgnoredDuringExecution
- Key: color
- value: blue

```bash
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue
spec:
  replicas: 3
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: color
                operator: In
                values:
                - blue
```

Create a new deployment named `red` with the `nginx` image and `2` replicas, and ensure it gets placed on the `controlplane` node only.

Use the label key - `node-role.kubernetes.io/control-plane` - which is already set on the `controlplane` node.

```bash
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: red
spec:
  replicas: 2
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role.kubernetes.io/control-plane
                operator: Exists
```

### Monitoring

```bash
controlplane kubernetes-metrics-server on  master ➜  k top node
NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
controlplane   249m         0%     1158Mi          0%        
node01         18m          0%     300Mi           0%
```

```bash
kubectl top node --sort-by='memory' --no-headers | head -1

kubectl top pod --sort-by='cpu' --no-headers | tail -1
```

### Resource Requirement and Limit

The status `OOMKilled` indicates that it is failing because the pod ran out of memory. Identify the memory limit set on the POD.

```bash
 State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       OOMKilled
      Exit Code:    1
      Started:      Mon, 08 Jan 2024 10:23:13 +0000
      Finished:     Mon, 08 Jan 2024 10:23:13 +0000
    Ready:          False
```

The `elephant` pod runs a process that consumes 15Mi of memory. Increase the limit of the `elephant` pod to 20Mi.

```bash
spec:
  containers:
  - args:
    - --vm
    - "1"
    - --vm-bytes
    - 15M
    - --vm-hang
    - "1"
    command:
    - stress
    image: polinux/stress
    imagePullPolicy: Always
    name: mem-stress
    resources:
      limits:
        memory: 20Mi
      requests:
        memory: 5Mi
```

### DaemonSet

On how many nodes are the pods scheduled by the **DaemonSet** `kube-proxy`?

```bash
k describe daemonset kube-proxy --namespace=kube-system
```

Deploy a **DaemonSet** for `FluentD` Logging.

1. create Deployment file
2. edit `kind` and requirements

### Static Pod

- kubelet이 node를 독립적으로 관리
- 다른 클러스터의 도움 없이 kubelet 자체로 생성된 pod들

What is the path of the directory holding the static pod definition files?

1. kubelet 설정 파일 위치 확인

```bash
controlplane ~ ➜  ps -aux | grep /usr/bin/kubelet

root        4689  0.0  0.0 3627016 106128 ?      Ssl  06:34   0:12 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock --pod-infra-container-image=registry.k8s.io/pause:3.9
root        9000  0.0  0.0   6748   720 pts/1    S+   06:44   0:00 grep --color=auto /usr/bin/kubelet
```

- `--config=/var/lib/kubelet/config.yaml`
1. 파일 내에서 `staticpod` 키워드 검색

```bash
controlplane ~ ➜  grep -i staticpod /var/lib/kubelet/config.yaml
staticPodPath: /etc/kubernetes/manifests
```

How many pod definition files are present in the manifests directory?

```bash
controlplane ~ ➜  cd /etc/kubernetes/manifests/

controlplane /etc/kubernetes/manifests ➜  ls
etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
```

- etcd
- kube-apiserver
- kube-controller-manager
- kube-scheduler

### Label

Apply a label `color=blue` to node `node01`

```bash
kubectl label node node01 color=blue
```