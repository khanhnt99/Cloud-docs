# Deployments Kubernetes
## ReplicaSet
- Trước khi đi vào `Deployment` ta phải tìm hiểu về ReplicaSet

 ![](https://raw.githubusercontent.com/khanhnt99/Kubernetes/master/images/replicaset.png)

### Tính năng:
- `ReplicaSet` sinh ra để đảm bảo số lượng Pod của 1 ứng dụng muốn duy trì trong 1 hệ thống

## Deployment
- `Pods` không thực hiện việc `self-heal`, `scale`, `updates` hoặc `rollbacks`. `Deployment` sẽ làm công việc này.
- `Deployments` có thể manage nhiều `replicas` của cùng 1 POD.
- `Deployment` là objects chính trong Kubernetes API, được xác định trong file manifest khi ta POST lên API Server.
- `Deployment` sử dụng `ReplicaSet` để cung cấp `self-healing` và `scalability`.

![](https://raw.githubusercontent.com/khanhnt99/Kubernetes/master/images/deployment.png)

- Đặc biệt `Deployment` cho phép cung cấp tính năng `rolling update` và `rollout update`.
- Với `rolling update` cung cấp tính năng tự cập nhật mà không có downtime.
- Còn `rollout update` có thể rollback 1 version cảu deployment.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-deployment
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

```
corgi@ubuntu:~/kubernetes$ kubectl get deploy -o wide
NAME               READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS         IMAGES         SELECTOR
mysql-1592048710   0/1     1            0           105d   mysql-1592048710   mysql:5.7.30   app=mysql-1592048710,release=mysql-1592048710
nginx-deployment   3/3     3            3           15s    nginx              nginx          app=nginx-deployment
```
- Hiển thị các thông tin của `deployment`

```
corgi@ubuntu:~/kubernetes$ kubectl describe deployment nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Sun, 27 Sep 2020 00:14:00 +0700
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx-deployment
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-deployment
  Containers:
   nginx:
    Image:        nginx
    Port:         80/TCP
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
NewReplicaSet:   nginx-deployment-7d56898d6b (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m25s  deployment-controller  Scaled up replica set nginx-deployment-7d56898d6b to 3
```

- Thực hiện `rolling update`: update lên version 1.13.10

```
corgi@ubuntu:~/kubernetes$ kubectl set image deployment/nginx-deployment nginx=nginx:1.13.10
deployment.extensions/nginx-deployment image updated
corgi@ubuntu:~/kubernetes$ kubectl rollout status deploy/nginx-deployment
deployment "nginx-deployment" successfully rolled out
corgi@ubuntu:~/kubernetes$ 
```

- Sau khi update

```
corgi@ubuntu:~/kubernetes$ kubectl describe deployment nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Sun, 27 Sep 2020 00:14:00 +0700
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx-deployment
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-deployment
  Containers:
   nginx:
    Image:        nginx
    Port:         80/TCP
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
NewReplicaSet:   nginx-deployment-7d56898d6b (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m25s  deployment-controller  Scaled up replica set nginx-deployment-7d56898d6b to 3
corgi@ubuntu:~/kubernetes$ kubectl set image deployment/nginx-deployment nginx=nginx:1.13.10
deployment.extensions/nginx-deployment image updated
corgi@ubuntu:~/kubernetes$ kubectl rollout status deploy/nginx-deployment
deployment "nginx-deployment" successfully rolled out
corgi@ubuntu:~/kubernetes$ kubectl describe deployment nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Sun, 27 Sep 2020 00:14:00 +0700
Labels:                 app=nginx-deployment
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=nginx-deployment
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-deployment
  Containers:
   nginx:
    Image:        nginx:1.13.10
    Port:         80/TCP
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
NewReplicaSet:   nginx-deployment-865cd45f9d (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  7m15s  deployment-controller  Scaled up replica set nginx-deployment-7d56898d6b to 3
  Normal  ScalingReplicaSet  67s    deployment-controller  Scaled up replica set nginx-deployment-865cd45f9d to 1
  Normal  ScalingReplicaSet  54s    deployment-controller  Scaled down replica set nginx-deployment-7d56898d6b to 2
  Normal  ScalingReplicaSet  54s    deployment-controller  Scaled up replica set nginx-deployment-865cd45f9d to 2
  Normal  ScalingReplicaSet  47s    deployment-controller  Scaled down replica set nginx-deployment-7d56898d6b to 1
  Normal  ScalingReplicaSet  47s    deployment-controller  Scaled up replica set nginx-deployment-865cd45f9d to 3
  Normal  ScalingReplicaSet  40s    deployment-controller  Scaled down replica set nginx-deployment-7d56898d6b to 0

```

- Sau khi update thông số **revision: 2**
- Check các bản history của `deployment`

```
corgi@ubuntu:~/kubernetes$ kubectl rollout history deployment/nginx-deployment  --revision=1
deployment.extensions/nginx-deployment with revision #1
Pod Template:
  Labels:	app=nginx-deployment
	pod-template-hash=7d56898d6b
  Containers:
   nginx:
    Image:	nginx
    Port:	80/TCP
    Host Port:	0/TCP
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>
```

- Thực hiện `rollback` phiên bản cũ:

```
corgi@ubuntu:~/kubernetes$ kubectl rollout undo deployment/nginx-deployment --to-revision=1
deployment.extensions/nginx-deployment rolled back

```

```
corgi@ubuntu:~/kubernetes$ kubectl get deploy -o wide
NAME               READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS         IMAGES         SELECTOR
mysql-1592048710   0/1     1            0           105d   mysql-1592048710   mysql:5.7.30   app=mysql-1592048710,release=mysql-1592048710
nginx-deployment   3/3     3            3           13m    nginx              nginx          app=nginx-deployment
```

__Docs__
- https://github.com/khanhnt99/Linux/tree/master/Kubernetes/Concepts
- https://github.com/khanhnt99/Kubernetes/tree/master/docs







