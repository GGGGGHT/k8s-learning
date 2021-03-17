[toc]

k8s作用： 
- 容器调度
- 容器编排
- 容器管理 

### k8s 基础命令

kubectl get pods 查看已存在的pods

kubectl get all 查看所有的

kubectl proxy 运行一个代理

kubectl logs $POD_NAME 查看pod的日志 

kubectl exec $POD_NAME [command] 在指定的pod中执行命令

```txt
$ kubectl exec kubernetes-bootcamp-765bf4c7b4-rn4nm env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=kubernetes-bootcamp-765bf4c7b4-rn4nm
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
NPM_CONFIG_LOGLEVEL=info
NODE_VERSION=6.3.1
HOME=/root
```

kubectl get services 查看当前集群中的service

kubectl expose deployment/{service_name} --type=[type] --port [port] 创建新的服务并导出 

kubectl describe services/{service_name} 查看服务信息 

```shell
$ kubectl describe services/kubernetes-bootcamp
Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   run=kubernetes-bootcamp
Annotations:              <none>
Selector:                 run=kubernetes-bootcamp
Type:                     NodePort
IP:                       10.110.10.170
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  31880/TCP
Endpoints:                172.18.0.5:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

kubectl describe deployment  

```shell
$ kubectl describe deployment
Name:                   kubernetes-bootcamp
Namespace:              default
CreationTimestamp:      Tue, 16 Mar 2021 06:48:47 +0000
Labels:                 run=kubernetes-bootcamp
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=kubernetes-bootcamp
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  run=kubernetes-bootcamp
  Containers:
   kubernetes-bootcamp:
    Image:        gcr.io/google-samples/kubernetes-bootcamp:v1
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   kubernetes-bootcamp-765bf4c7b4 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  4m    deployment-controller  Scaled up replica set kubernetes-bootcamp-765bf4c7b4 to 1
```



kubectl delete {service_name} 删除service 删除这个service后程序已经无法从外部访问，但该服务依然存在。且正常运行

kubectl scale deployment/{serve_name} --replicas={size} 副本扩容到{size} 





---

### pod生命周期

Pending --> Running --> Succeeded | Failed

`Pending`: 起始阶段 pod已被k8s系统接收，但有一个或多个容器尚未创建也未运行。此阶段包括等待pod被高度的时间和通过网络下载镜像的时间。

`Running`: pod已经绑定到了某个节点，所有容器都心被创建。至少其中有一个容器仍在运行，或者正处于启动或重启状态

`Succeed`: pod中所有容器都已成功终止。并且不会再重启 

`Failed`:  所有容器都已经终止，并且至少有一个容器是因为失败终止。也就是说容器以非0状态退出或者被系统终止。

---

### 容器状态 

kubectl describe pod $POD_NAME 查看pod运行状态

```txt
$ kubectl describe pod $POD_NAME
Name:         kubernetes-bootcamp-765bf4c7b4-7gd2s
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.20
Start Time:   Tue, 16 Mar 2021 03:51:39 +0000
Labels:       pod-template-hash=765bf4c7b4
              run=kubernetes-bootcamp
Annotations:  <none>
Status:       Running
IP:           172.18.0.3
IPs:
  IP:           172.18.0.3
Controlled By:  ReplicaSet/kubernetes-bootcamp-765bf4c7b4
Containers:
  kubernetes-bootcamp:
    Container ID:   docker://fcd5259421a6ccb2c473ccb939169918105f53ab7c9694e95fb10f3e3ecb61f2
    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 16 Mar 2021 03:51:41 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-jzzdz (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-jzzdz:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-jzzdz
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason            Age   From               Message
  ----     ------            ----  ----               -------
  Warning  FailedScheduling  16s   default-scheduler  0/1 nodes are available: 1 node(s) had taints that the pod didn't tolerate.
  Normal   Scheduled         10s   default-scheduler  Successfully assigned default/kubernetes-bootcamp-765bf4c7b4-7gd2s to minikube
  Normal   Pulled            8s    kubelet, minikube  Container image "gcr.io/google-samples/kubernetes-bootcamp:v1" already present on machine
  Normal   Created           8s    kubelet, minikube  Created container kubernetes-bootcamp
  Normal   Started           8s    kubelet, minikube  Started container kubernetes-bootcamp
```



Waiting:

Running:

Terminated: 



在Pod运行期间，`Kubectl`能够重启窗口以处理一些失效场景。





---

service

