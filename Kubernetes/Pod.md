# Pod
## 1. Khái niệm:
- Là đơn vị nhỏ nhất trong `Kubernetes`.
- `Pod` được cấu thành từ 1 `container` hoặc nhiều `container`.
- Các `container` trong cùng 1 Pod sẽ chia sẻ các resource sau:
  + IP address
  + Ports
  + Hostname
  + Sockets
  + Memory
  + Volumes

![](https://d33wubrfki0l68.cloudfront.net/fe03f68d8ede9815184852ca2a4fd30325e5d15a/98064/docs/tutorials/kubernetes-basics/public/images/module_03_pods.svg)

![](https://camo.githubusercontent.com/1422ff7859a3b8928ec430bebffa07d9a7fbaa62/68747470733a2f2f692e696d6775722e636f6d2f6e676d68376b312e706e67)

- Bản thân Pod có tài nguyên riêng về file system, cpu, ram, volumes, địa chỉ network.

![](https://www.upsieutoc.com/images/2020/09/15/Screenshot-from-2020-09-15-22-45-47.png)

![](https://www.upsieutoc.com/images/2020/09/15/Screenshot-from-2020-09-15-22-59-21.png)

- `Container` trong 1 pod đại diện cho các tiến trình mà có thể chạy trên cùng `1 máy nếu chưa có container`.
- Các `container` có thể truy cập vào các `container` khác trong 1 `pod` dưới dạng cổng khác nhau trên `localhost`.

## 2. Flat inter-pod network 

![](https://i.ibb.co/D5gWT7V/Screenshot-from-2020-09-21-16-08-59.png)

- Mỗi `container` trong 1 `pod` có thể giao tiếp với nhau qua `flat NAT-less network`

## 3. Tạo 1 Pod 

```
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod
spec:
  containers:
    - name: nginx-container
      image: nginx:1.12
      ports:
      - containerPort: 80

```

```
corgi@ubuntu:~/kubernetes$ kubectl apply -f pod_sample.yaml 
pod/sample-pod created

trong đó
apiVersion: cho biết API group và API version để tạo object
kind: nói cho Kubernetes loại Object được deployed
metadata: là nơi attach name và labels
spec: xác định container chạy trong Pod
```



```
corgi@ubuntu:~/kubernetes$ kubectl get pod -o wide
NAME                                READY   STATUS    RESTARTS   AGE    IP            NODE                                  NOMINATED NODE   READINESS GATES
mysql-1592048710-6d7c7b5c67-nxqmz   0/1     Pending   0          101d   <none>        <none>                                <none>           <none>
sample-pod                          1/1     Running   0          27s    10.200.0.11   tuda-wx9tr9d1n2k1qndd-node-n7xgreai   <none>           <none>
```

## 3. Scaling Pods

![](https://camo.githubusercontent.com/b7f3dfe5b7eea1b7e3d376c581ef3661c9e583d3/68747470733a2f2f692e696d6775722e636f6d2f446479626653492e706e67)

## 4. Pod lifecycle
- `Pod lifecycle` được xác định trong `YAML manifest file` và `Post manifest` tới API server.
- Khi được lên lịch trên 1 node, trạng thái của nó là `pending`. Khi mọi thứ sẵn sàng chuyển sang trạng thái `running`, sau đó chuyển sang trạng thái `succeed` khi hoàn thành các tasks.

![](https://camo.githubusercontent.com/aa346316eba2f16544b81ab891402476f66b6d19/68747470733a2f2f692e696d6775722e636f6d2f357a49537269492e706e67)

- `kubectl get pods --watch` : xem trạng thái khi thay đổi
- `pods` được monitor bằng `kubelet` (`kubelet` chạy trên các node)

__Docs__
- https://github.com/khanhnt99/Linux/tree/master/Kubernetes/Concepts
- https://github.com/khanhnt99/Kubernetes/tree/master/docs
