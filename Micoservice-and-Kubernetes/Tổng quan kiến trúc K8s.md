# Pods

![[Micoservice-and-Kubernetes/attachment/Pods.png]]

Pods là một nhóm của một hoặc nhiều container ứng dụng (như là Docker) và bao gồm `shared storage`, Tài nguyên của chúng là:
- Shared storage, như là Volumes.
- Networking, như là một địa chỉ IP độc quyển của cluster.
- Thông tin về cách nào để chạy container.

**Một Pod mô hình hóa một “máy chủ logic” dành riêng cho ứng dụng và có thể chứa các container ứng dụng khác nhau vốn tương đối gắn kết chặt chẽ với nhau.**
các container trong một Pods chia sẻ cùng một địa chỉ IP và không gian cổng luôn được đồng vị trí và đồng lập lịch, và chạy tỏng cùng một ngữ cảnh trên cùng một Node.
**Pod là đơn vị nguyên tử trên nền tảng Kubernetes.** Khi chúng ta tạo một Deployment trong Kubernetes, Deployment đó sẽ tạo ra các Pod chứa container bên trong (thay vì tạo container trực tiếp). Mỗi Pod được gắn với Node nơi nó được lập lịch, và tồn tại ở đó cho đến khi kết thúc (theo chính sách khởi động lại) hoặc bị xóa. Trong trường hợp một Node gặp sự cố, các Pod giống hệt sẽ được lập lịch lại trên các Node khả dụng khác trong cluster.


# Nodes

![[Micoservice-and-Kubernetes/attachment/Nodes.png]]

Một pod thường chạy trên Node, và Node là một worker machine trong Kubernetes và có thể là một máy ảo hoặc máy vật lý. Mỗi Node được quản lí bởi `control plane` .
Một Node có thể có nhiều pods, và K8s control plane tự động xử lý schedulng các một thông qua Node trong cluster.
Mỗi Kubernetes Node bao gồm 2 thành phần:
- Kubelet, là tiến trình chịu trách nhiệm cho giao tiếp giữa các K8s control plane và Node, Nó quản lý Pod và các containers trên machine.
- Container runtime (như Docker) chịu trách nhiệm cho kéo container image từ registery, và unpacking the container và runing the application.
- 
