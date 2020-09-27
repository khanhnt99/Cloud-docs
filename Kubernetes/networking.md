# Network trong Kubernetes

## 1. Giới thiệu:
- Network trong `Kubernetes` gồm có 4 phần:
  + `Network to Pod`
  + `Pod to Pod communications`
  + `Pod to Service communications`
  + `External to Service communication`

## 2. Network to Pod
- Mỗi Pod trong K8s có 1 IP
- IP của 1 pod được chia sẻ bởi tất cả các container trong Pod.
- Mặc định các Pod có thể được kết nối với nhau trong 1 cụm K8s.
- `Container Pause`:
  + Chạy trên tất cả các node trong K8s
  + Gọi là `sandbox container` có nhiệm vụ lưu trữ `network namespace`được chia sẻ bởi tất cả các container trong 1 pod.
  + Với cơ chế này, trong 1 Pod khi container chết hoặc 1 container mới được tạo thì IP Pod sẽ không bị thay đổi.

![](https://raw.githubusercontent.com/khanhnt99/Kubernetes/master/images/containerpause.png)

## 3. Pod to Pod communications
- Để kết nối giữa các pods, ta sử dụng 1 network plugin ngoài (`Flannel`)

### 3.1 Flannel:
#### Khái niệm:
- `Flannel` là một giải pháp overlay network được sử dụng để cấu hình network trong K8s.
- Cơ chế `overlay network` sử dụng backend (VXLAN) để đóng gói `packet in packet` đi qua underlay networks.
- `Flannel` chạy 1 tiến trình agent (`flanned`) trên tất cả các node trên cụm K8s. Tiến trình chịu trách nhiệm phân bổ `subnet lease` trên mỗi node.
- `Flannel` lưu tất cả thông tin vào  `etcd`: 
   + Cấu hình mạng
   + Phân bổ Subnet 
   + Ip public của host

#### Kết nối giữa các Pod trên 1 node

![](https://raw.githubusercontent.com/khanhnt99/Kubernetes/master/images/rootnetworkns.png0)

- Trên mỗi node trong cụm K8s (OS Linux) sẽ có 1 `root network namespace`. Interface chính `eth0` nằm trong `root network namespace` này.
- Mỗi Pod cũng có 1 `network namespace` riêng với 1 `virtual ethernet (veth)` kết nối nó tới `root network namespace`.
- `veth` là đường kết nối giauwx `root network namespace` và `pod network namspace`.

![](https://raw.githubusercontent.com/khanhnt99/Kubernetes/master/images/podnetns1.png)

- Với plugin network `Flannel` sẽ sử dụng 1 bridge `cbr0` để các pod nói chuyện với nhau. 

![](https://raw.githubusercontent.com/khanhnt99/Kubernetes/master/images/podnetns.gif)

Traffic flows:
- Gói tin đi từ Pod1 vào `root netns` tại `vethxx`.
- Gói tin được chuyển tới bridge `cbr0`, sử dụng giao thức `ARP request` để phát hiện đích Pod nào có IP này.
- Gói tin gửi từ `cbr0` chuyển tiếp đến `vethyy` đến pod 2.

#### Kết nối giữa các Pod trên các node khác nhau
- Mỗi node trong 1 cụm K8s sẽ được gán 1 `CIDR block` (1 dải địa chỉ IP) để cấp cho các Pods.
- Vì vậy mỗi Pod có 1 IP duy nhất và không bị `conflict` với các Pods trên tất cả các node khác trong cụm. 

![](https://raw.githubusercontent.com/khanhnt99/Kubernetes/master/images/flannel.gif)

- Traffic gói tin từ pod 1 đến pod 4:
  + Gói tin từ `pod 1 netns` tại `eth0 của pod 1` sẽ được gửi vào `root netns` tại `vethxxx`.
  + Gói tin chuyển tới `cbr0` và gửi `ARP request` để tìm `destination` của gói tin.
  + Vì không `pod` nào trên node có địa chỉ của `IP pod4` nên `cbr0` gửi tới `flannel0`.
     - `Route table` của node khi cấu hình `flannel0` được lưu ở `etcd`.
     - `flanned daemon` đọc thông tin trong `etcd` sẽ có IP của tất cả các Pod và các dải IP mà Pod đang ở trong node nào. `Flannel` ánh xạ các IP pod với IP các node

