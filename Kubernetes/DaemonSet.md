# DaemonSet

## 1. Giới thiệu
- **DaemonSet** có thể coi là 1 bản sao đặc biệt của `ReplicaSet`.
- `ReplicaSet` sẽ bố trí tổng số XXX Pod trên các Node trong Kubernetes phù hợp với từng Node => Số lượng các Pod trong 1 Node có thể `không bằng nhau`và `không hẳn là phân bố trong từng Node`.
- **DaemonSet** là loại resource phân bố Pod một cách tuần tự từng Pod trên tất cả các Node.
- Trường hợp dùng DaemonSet như:
   + Khi ta cần 1 chương trình nào đó nhất định phải chạy trên tất cả các Node.
   + Example: 
     - Fluentd: Collect log xuất ra từ các Pod
     - Dataloag
     
![](https://i.pinimg.com/originals/84/46/82/8446821e0e5564db4dbfbceffdabc5cd.png)

## 2. Tạo 1 DaemonSet

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: sample-daemon
spec:
  selector:
    matchLabels:
      app: sample-app
  template:
    metadata:
      labels:
        app: sample-app
    spec:
      containers:
        - name: nginx-container
          image: nginx:1.12
```

```
corgi@ubuntu:~/kubernetes$ kubectl create -f daemonSet.yaml 
daemonset.apps/sample-daemon created
corgi@ubuntu:~/kubernetes$ vim daemonSet.yaml
corgi@ubuntu:~/kubernetes$ kubectl get pod -o wide
NAME                                READY   STATUS    RESTARTS   AGE    IP            NODE                                  NOMINATED NODE   READINESS GATES
mysql-1592048710-6d7c7b5c67-nxqmz   0/1     Pending   0          105d   <none>        <none>                                <none>           <none>
sample-daemon-kwl52                 1/1     Running   0          63s    10.200.0.27   tuda-wx9tr9d1n2k1qndd-node-n7xgreai   <none>           <none>
```

__ Tài liệu tham khảo__

- https://blog.vietnamlab.vn/2020/05/05/nhap-mon-kubernetes-p5-kubernetes-workloads-resource-2/
- https://github.com/khanhnt99/Linux/tree/master/Kubernetes/Concepts
- https://github.com/khanhnt99/Kubernetes/tree/master/docs

