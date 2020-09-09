# Cài đặt Nginx trên K8s

## Bước 1:
- Truy cập vào cluster
  + `export KUBECONFIG=$KUBECONFIG:wx9tr9d1n2k1qndd.kubeconfig`

## Bước 2: Khởi tạo file .yaml

+ `vim nginx-deployment-service.yaml`

```
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment 
spec:
# luon tao ra 2 pods luon chay
  replicas: 2
  selector:
    matchLabels:
      app: nginx-deployment
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
        - name: nginx-deployment
          image: nginx 
        #image cua container docker
          ports: 
          - containerPort: 8080
---

apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  ports:
  # port ben ngoai ma cac pods co the giao tiep voi nhau
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    # dua theo app : nginx-deployment phia tren se map voi service nay
    app: nginx-deployment
  type: ClusterIP
```

- Chạy lệnh
  + `kubectl apply -f nginx-deployment-service.yaml`

```
corgi@ubuntu:~/kubernetes$ kubectl apply -f nginx-deployment-service.yaml 
deployment.apps/nginx-deployment created
service/nginx-svc created
```
- Kiểm tra xem đã cài đặt chưa

```
corgi@ubuntu:~/kubernetes$ kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
mysql-1592048710   0/1     1            0           88d
nginx-deployment   2/2     2            2           25s
```
```
corgi@ubuntu:~/kubernetes$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
mysql-1592048710-6d7c7b5c67-nxqmz   0/1     Pending   0          88d
nginx-deployment-7756879896-8q74g   1/1     Running   0          117s
nginx-deployment-7756879896-vtqhb   1/1     Running   0          117s
```
```
corgi@ubuntu:~/kubernetes$ kubectl get svc
NAME               TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
kubernetes         ClusterIP   10.93.0.1     <none>        443/TCP    88d
mysql-1592048710   ClusterIP   10.93.0.142   <none>        3306/TCP   88d
nginx-svc          ClusterIP   10.93.0.198   <none>        80/TCP     2m19s
```
```
corgi@ubuntu:~/kubernetes$ kubectl describe svc/nginx-svc
Name:              nginx-svc
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=nginx-deployment
Type:              ClusterIP
IP:                10.93.0.198
Port:              http  80/TCP
TargetPort:        8080/TCP
Endpoints:         10.200.0.5:8080,10.200.0.6:8080
Session Affinity:  None
Events:            <none>
```
```
corgi@ubuntu:~/kubernetes$ kubectl edit  svc/nginx-svc
service/nginx-svc edited
corgi@ubuntu:~/kubernetes$ kubectl get svc/nginx-svc
NAME        TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
nginx-svc   NodePort   10.93.0.198   <none>        80:31098/TCP   4m5s
```

## 4. Tổng hợp:
- `Replicas = 2` đảm bảo 1 số lượng pod luôn = 2, khi pod bị down ngay lập tức khởi tạo pod mới.
- Các Pods có chung 1 dải IP `10.200.0.0` (đây là 1 virtual network ảo để các pod có thể giao tiếp với nhau)
- Các IP của pod + port 8080 -> IP service

```
corgi@ubuntu:~/kubernetes$ kubectl describe svc/nginx-svc
Name:              nginx-svc
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=nginx-deployment
Type:              ClusterIP
IP:                10.93.0.198
Port:              http  80/TCP
TargetPort:        8080/TCP
Endpoints:         10.200.0.7:8080,10.200.0.8:8080
Session Affinity:  None
Events:            <none>
```



__Tài liệu tham khảo__

 
- https://viblo.asia/p/su-dung-kubernetes-ingress-nginx-ingress-controller-de-dinh-tuyen-nhieu-service-khac-nhau-L4x5x8Gg5BM
- https://viblo.asia/p/deploy-va-tao-service-nginx-tren-kubernetes-6J3ZgxwAlmB
- https://lazyadmin.info/setup-ingress-tren-kubernetes-bang-nginx-controller/



