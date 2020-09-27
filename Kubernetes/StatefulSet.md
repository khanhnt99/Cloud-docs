# StatefulSet
## 1. Giới thiệu:
- Là 1 resource trong K8s 
- Là 1 bản sao đặc biệt của `ReplicaSet`
- Khác với `ReplicaSet` tại những điểm sau:
  + Tên các Pod được đánh index bằng số:
    - sample-statefulset-1, sample-statefulset-2,...,sample-statefulset-N
   + Cơ chế để cố định hóa
     - Tên pod không thay đổi
     - Sử dụng Persistent Volume thì nó sẽ Recreate trên cùng 1 ổ đĩa.

## 2. Tạo 1 StatefulSet

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: sample-app
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: sample-app

---

apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: sample-statefulset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: sample-app
  serviceName: nginx
  template:
    metadata:
      labels:
        app: sample-app
    spec:
      containers:
        - name: nginx-container
          image: nginx:1.12
          ports:
           - containerPort: 80
```

```
corgi@ubuntu:~/kubernetes$ kubectl get statefulSet -o wide
NAME                 READY   AGE   CONTAINERS        IMAGES
sample-statefulset   3/3     62s   nginx-container   nginx:1.12
corgi@ubuntu:~/kubernetes$ kubectl get pod
NAME                                READY   STATUS    RESTARTS   AGE
mysql-1592048710-6d7c7b5c67-nxqmz   0/1     Pending   0          105d
sample-statefulset-0                1/1     Running   0          78s
sample-statefulset-1                1/1     Running   0          76s
sample-statefulset-2                1/1     Running   0          74s
```

- Các Pod có gán index thứ tự 0,1,2

- Pod được tạo bởi `StatefulSet` không phân bố đồng thời vào các node, nó sẽ create từng pod một. Khi nào `POD` ở trạng thái Ready thì Pod thứ 2 mới được create. 
- Khi xóa pod sẽ xóa pod có index lớn nhất. 

__Tài liệu tham khảo__

- https://blog.vietnamlab.vn/2020/05/05/nhap-mon-kubernetes-p5-kubernetes-workloads-resource-2/
