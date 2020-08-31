# Tổng quan về Kubernetes
## 1. Giới thiệu
- `Kubernetes là 1 phần trong hệ sinh thái của container ngày nay`
- Hệ sinh thái của container gồm 3 phần chính
  + Phần 1: Các thành phần cơ bản và kiến trúc lõi của **container** 
    - Runtime spec
    - Image format speck
    - Images
    - Network
    - Storage
  + Phần 2: Các công nghệ về platform liên quan tới container
    - Gồm các sản phẩm orchestration container (khởi tạo các container theo cơ chế điều phối)
    - Nền tảng quản lí container
    - Nền tảng PaaS dựa trên container
  + Phần 3: Các công nghệ hỗ trợ container
    - Công nghệ triển khai trên nhiều máy chủ vật lí.
    - Giải pháp thu thập log và giám sát

![](https://raw.githubusercontent.com/hocchudong/ghichep-kubernetes/master/images/Docker-ecosys1.png)

__Note:__
- Kubernetes nằm trong phần 2 của hệ sinh thái container.
- Đóng vai trò là công cụ orchestration **(Tạo ra, điều phối, chỉ huy các container)**

## 2. Khái niệm
### 2.1 POD
- Là 1 nhóm (>=1) các container chứa ứng dụng cùng chia sẻ tài nguyên lưu trữ, địa chỉ IP, ...
- Pod có thể chạy theo 2 cách:
  + Pods that run a single container: 1 container ứng với 1 pod
  + Pods that run multiuple containers: 1 pod có thể là 1 ứng dụng bao gồm nhiều container kết nối và chia sẻ tài nguyên với nhau
- Tài nguyên chia sẻ trong pod:
  + Storage: Pod chỉ định 1 `shared storage volumes`(các container có thể truy cập vào volume này)
  + Networking: 
     - Mỗi pod `cấp 1 địa chỉ IP`. Container trong 1 POD cùng chia sẻ **network namespace** (Địa chỉ IP và Port)
     - Các container trong cùng 1 POD có thể giao tiếp với nhau và có thể giao tiếp với container ở POD khác (use the shared network resource)

### 2.2 Replication Controller
- Đảm bảo số lượng Pod replicas đã định nghĩa luôn luôn chạy đủ số lượng tại bất kì thời điểm nào.
- Thông qua Replication Controller, K8s sẽ quản lí **vòng đời** của POD (scalling up and down, rollling deployments, monitoring)

### 2.3 Services
- POD có tuổi thọ ngắn nên không đảm bảo về địa chỉ IP mà chúng được cấp.
- Service là khái niệm thực hiện bởi:
  + Domain name
  + Port
- Service sẽ tự động tìm các POD được đánh **label** phù hợp (trùng label service) rồi chuyển connection tới đó.
- Đây là nơi có thể cấu hình cân bằng tải và `expose` các pod đó.

### 2.4 Volume
- Nơi các container có thể truy cập và lưu trữ thông tin.
- Persistent Volume (PV): Khái niệm đưa ra 1 dung lượng thực tế.
- Persistent Volume claim (PVC): khái niệm ảo, đưa ra 1 dung lượng cần thiết mà ứng dụng yêu cầu.

### 2.5 Namespaces
- Hoạt động cơ chế nhóm trong Kubernetes
- Các services, pods, replication controller, volumes có thể bên trong 1 namespace.
- Cung cấp mức độ cô lập với các phần khác của cluster.

### 2.6 Config Map - Secret
- Secret dùng để lưu trữ các mật khẩu, token, những thứ cần bảo mậtật.

## 3. Thành phần 

![](https://camo.githubusercontent.com/7ff2160c4f3e5fbdceabdf2014aa2156ddd6f416/68747470733a2f2f73332d75732d776573742d322e616d617a6f6e6177732e636f6d2f782d7465616d2d67686f73742d696d616765732f323031362f30362f6f376c656f6b2e706e67)

### 3.1 Master node
- Quản lí Kubernetes Cluster
- Cấu hình các nhiệm vụ cần thực hiện
- Quản lí, điều hành các worker node

### 3.2 API server
- Tiếp nhận các lệnh **REST** để kiểm soát cluster.
- Xử lí các yêu cầu, xác nhận chúng, thực hiện các ràng buộc.

### 3.3 Etcd Storage
- Cơ sở dữ liệu key-value, phân phối và nhất quán sử dụng tìm kiếm dịch vụ

### 3.4 Scheduler
- Triển khai các pods, services lên các nodes
- Quyết định nơi triển khai 1 dịch vụ cụ thể

### 3.5 Controller manager
- Thay đổi trạng thái hiện tại của cluster thành trạng thái mong muốn.

### 3.6 Worker node
- Nơi POD chạy
- Chứa các dịch vụ cần thiết quản lí kết nối mạng giữa các container

### 3.7 Kubelet
- Lấy cấu hình thông tin pod từ API server, đảm bảo container up và running
- Liên lac với master node
- Liên lac etcd

### 3.8 Kube-proxy 
- Proxy mạng và cân bằng tải cho dịch vụ trên worker node.

### 3.9 Kubectl 
- Giao diện dòng lệnh giao tiếp với API service
- Gửi lệnh đến master node.
 
## Tài liệu tham khảo:
- https://github.com/hocchudong/ghichep-kubernetes/blob/master/notes/concepts.md
- https://github.com/hocchudong/ghichep-kubernetes/blob/master/docs/kubernetes-5min/01.Gioithieuve_Kubernetes.md


