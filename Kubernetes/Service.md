# Kubernetes Services
- `Services REST` trong Kubernetes API.
- Giống `Pod`, `ReplicaSet`, `Deployment`, `Kubernetes Service` là 1 object trong API có thể xác định trong mainifest.
- Mỗi `Service` đều có `IP`, `DNS name` và `port` riêng của nó.
- `Services` sử dụng labels để tự động chọn Pods trong cluster chúng sẽ gửi traffic tới.

## Cách `Kubernetes Service` cung cấp networking tới 1 Pods động
- `Pods` và `Services` được ghép thông qua labels và label selectors

![](https://camo.githubusercontent.com/e641a21015660919e18b91e3164760d52120666e/68747470733a2f2f692e696d6775722e636f6d2f6e5737573136652e706e67)

![](https://camo.githubusercontent.com/7e53d20c7aeb8686608c1f837e6a05db8fc7dd68/68747470733a2f2f692e696d6775722e636f6d2f4362345678464e2e706e67)

## Services và Endpoint Objects


## Các loại Service trong Kubernetes
### 1. ClutserIP:
- Đây là loài Service default.
- 1 ClusterIP Service có 1 IP và Port chỉ có thể truy cập từ bên trong cluster.

### 2. NodePort Service:
- Được `build on top` của ClusterIP và cho phép `truy cập từ bên ngoài Cluster`.
- Khi tạo 1 Services và sử dụng Label để liên kết Pods. Service Object có `reliable NodePort được map với mỗi node trên cluster`.
- `Giá trị` NodePort `giống nhau` trên mỗi `node`.
- `Traffic` từ bên ngoài cluster có thể vào bất kì `node nào trong cluster` trên NodePort và tới `Pods`.

![](https://camo.githubusercontent.com/a86bcfe5c861fa519e6dd30e3bca63dd4f85865e/68747470733a2f2f692e696d6775722e636f6d2f6f6b32683237332e706e67)

```
Có 1 NodePort Service chứa 3 Pods exposed port 30050 trên mỗi node trong cluster
Client truy cập vào node 2 qua port 30050
Sau đó chuyển hướng tới Service Object
Service liên kết tới 1 Endpoint Object và luôn up-to-date danh sách Pods matching với label selector.
``` 
