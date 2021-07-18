
# kubernetes
phần này mình chỉ mới expose đc ra cluster, chưa expose đc ra mạng ngoài (xem phần 2 để biết chi tiết)

## I. Install
1. install docker
install docker từ apt/snap

2. install kubectl
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-other-package-management
```
snap install kubectl --classic
```

3. install minikube (có thể sử dụng kind, kubeadm)
https://minikube.sigs.k8s.io/docs/start/
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

4. start cluster
```
minikube start
```


khi run minikube start, mình bị lỗi docker root permission (do mình deploy trên 1 vm vultr) => cách fix (https://github.com/kubernetes/minikube/issues/8257#issuecomment-689128598)
```
minikube start --force --driver=docker
```

để add node (https://minikube.sigs.k8s.io/docs/commands/node/):
```
minikube node add
```


Ngoài việc tạo từng nodes, mình có tìm thấy hdan tạo multinodes: https://minikube.sigs.k8s.io/docs/tutorials/multi_node/


## II. Config & run

1. lỗi `ErrImageNeverPull` khi tạo pod (có thể thấy khi `kubectl get pods`) => image fai đc build ở minikube nodes, ko fai ở local. Có 2 hướng fix:
    - chuyển sang sử minikube docker trong current session bằng cách `eval $(minikube docker-env)` và build lại image (chú ý ko đc sử dụng `sudo` khi build docker image vì minikube ko chơi vs sudo): https://newbedev.com/getting-errimageneverpull-in-pods
    - copy image từ local sang minikube nodes => cách này mình chưa thử (https://stackoverflow.com/questions/49898535/kubernetes-fails-to-run-a-docker-image-build-locally)

    - có hiểu hơn phần này, có lẽ fai đọc thêm ở (chưa đọc): https://minikube.sigs.k8s.io/docs/handbook/pushing/


2. ko expose đc external-ip: 

- theo mình tìm hiểu thì chỉ khi dùng kubernetes do các cloud provider (AWS, GCP, Azure) thì mới có tích hợp load balancer này. Nếu sử dụng minikube thì mặc định sẽ k expose đc external-ip => vì vậy sau khi run, mặc định chỉ dùng đc minikube ip, k dùng đc ip localhost hay ip srv. 
    - sử dụng cluster-ip: `minikube service [service name]` hoặc `minikube ip` để xem ip và `kubectl get service` để xem port

- để tạo external-ip, mình có tìm đc 1 số hdan. Tuy nhiên cách này theo mình hiểu chỉ tạo external ip cho cluster, vẫn chưa dùng đc srv ip (cũng ko dùng đc localhost:port hay 127.0.0.1:port):
    - sử dụng `minikube tunnel` (chú ý là nếu ctrl+c thì external sẽ ko có nữa => fai mở 1 session terminal khác để curl) => sẽ tạo đc external-ip, truy cập đc qua external-ip, nhưng vẫn ko truy cập qua srv-ip đc (tham khảo https://minikube.sigs.k8s.io/docs/start/)
    - install MetalLB (mình thấy khá nhiều ng có hdan là dùng cái này nhưng chưa test) (https://stackoverflow.com/a/62441410 hoặc https://makeoptim.com/en/service-mesh/kubernetes-external-ip-pending)
    
- có vẻ dù tác gỉa dùng k8s local mà vẫn expose đc là do sử dụng docker desktop => mình đã tạo 1 [test project](https://github.com/sontt22791/k8s-hello-docker-desktop) dùng docker desktop trên win thì expose ra đc external-ip = localhost (curl localhost:6000 thì ok, mở bằng browser thì lỗi => có thể cần check thêm)
## III. link đã tham khảo:

- hdan về kubernetes => nên đọc để hiểu cơ bản: https://comdy.vn/kubernetes/gioi-thieu-ve-kubernetes/
- learn k8s basic with minikube: https://kubernetes.io/docs/tutorials/kubernetes-basics/scale/scale-intro/

- 1 số example hello-world mình đã dùng để test
    - install minikube & quick start (có vd về deploy NodePort và LoadBalancer sử dụng `minikube tunnel`): https://minikube.sigs.k8s.io/docs/start/
    - vd về loadbalancer - expose external ip: https://kubernetes.io/docs/tutorials/stateless-application/expose-external-ip-address/

- yaml: https://kubernetes.io/blog/2019/07/23/get-started-with-kubernetes-using-python/

- create external load balancer: https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/

- 1 số link tham khảo về minikube tunnel trick: 
    - https://makeoptim.com/en/service-mesh/kubernetes-external-ip-pending
    - quick start ở trên

- kubectl cheatsheet: https://kubernetes.io/docs/reference/kubectl/cheatsheet/



