# Practice Command

### Imperative command

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
    

### Node Affinity - KEEP

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