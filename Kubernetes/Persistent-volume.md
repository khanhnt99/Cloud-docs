# PersistentVolume
## 1. Giới thiệu

|Type of storage|How long does it last|
|---------------|---------------------|
|Container Filesystem|Container Lifetime|
|Volume|Pod Lifetime|
|Persistent volume|Cluster lifetime|

- Trong K8s, 1 pod có thể gồm nhiều container, để đảm bảo dữ liệu của các container tron 1 pod không bị mất đi khi 1 container nào đó restart, K8s sử dụng `volume`. `Các conatiner trong 1 pod dùng chung volume`.
- `Volume` là thành phần của Pod, tồn tại độc lập với các container trong Pod, nhưng Pod restart chuyển sang node khác thì dữ liệu không đảm bảo.
Để giải quyết K8s sử dụng `Persistent volume (PV)`.
- `Persistent volumes` là kiểu dữ liệu liên tục, tồn tại độc lập tới vòng đời của containers, pods và có thể là cả node trong cluster.
- 1 Pod không access trực tiếp với `Persistent volumes` mà sử dụng `Persistent volume claim (PVC)` để đọc và ghi dữ liệu tới `Persistent volume`.

![](https://raw.githubusercontent.com/khanhnt99/Kubernetes/master/images/persistentvolume.png)

## 2. Các bước triển khai các Pod sử dụng `Persistent Volume`
- Khởi tạo 1 Persistent Volume (nfs, glusternfs, ceph,...)
- Tạo 1 manifest `Persistent Volume Claim (PVC)` thực hiện `bound` với PV được tạo.
- Người dùng tạo `Pod` với volume sử dụng PVC.

__Docs__
- https://github.com/khanhnt99/Linux/tree/master/Kubernetes/Concepts
- https://github.com/khanhnt99/Kubernetes/tree/master/docs
