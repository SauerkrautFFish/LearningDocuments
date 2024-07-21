



node：一个物理机或者虚拟机，可以运行1-多个pod

pod：1-多个容器组合

service：将一组pod封装成一个服务，并提供一个统一入口访问这个服务

ingress：将集群外的路由集群内的service上，比如域名+端口号等

集群：多个node的组合？

configmap和secret：将配置信息和敏感信息封装起来供应用程序使用，让应用程序和配置信息解耦，secret是把配置信息做了base64编码

volume：将数据挂载到本地磁盘或远程存储上，防止pod销毁带来的数据丢失问题

deployment：部署无状态应用，可管理多个pod，具有副本控制，滚动更新，自动扩缩容

statefulset：部署有状态应用，可管理多个pod，具有副本控制，滚动更新，自动扩缩容





worker node有3个组件：kubelet，kube-proxy和container-runtime

kubelet：监控和管理pod，定期和api-server接受修改后的pod

kube-proxy：为pod对象提供网络代理和负载均衡服务

container-runtime：就是pod运行的环境



master node基本有4个组件：kube-apiserver，etcd，controllermanager和scheduler

kube-apiserver：负责提供k8s集群的api接口服务，所有的请求都会先经过这，再做分发，如kubectl客户端就是可以和apiserver通信，做一些创建更新删除pod请求

scheduler：负责监控集群中所有node的资源使用情况，比如说你要新增一个pod，它会看看哪台node上比较空闲，然后部署pod在这台node上。

controllermanager：监控和检测故障资源，并对故障进行处理，比如重启pod，node和service等

etcd：高可用键值存储系统，用来存储集群中所有资源对象的状态信息





minikube用来在本地搭建k8s环境

kubectl负责和k8s集群交互



```bash
kubectl get nodes //查看集群中节点信息
kubectl get svc //查看所有服务
kubectl get pod -o wide//查看所有pod wide可以看到更加多信息 比如说pod所在的node和ip地址等
kubectl get replicaset ...... //replicaset用来管理副本数，deployment管理replicaset，replicaset管理pod

kubectl run nginx123 --image=nginx //创建一个nginx pod
kubectl create deployment/service [自己取得service名或者deployment名] --image=xxx

kubectl edit deployment [deployment-name] // 会打开一个yaml文件 如修改了文件则会自动更新deployment

kubectl logs [pod-name] //查看这个pod的日志

kubectl exec -it [pod-name] -- /bin/bash // 交互式进入pod

kubectl get all // 查看所有资源 比如service deployment replicaset等

kubectl delete deployment [deployment-name] // 删除 service和其他资源也是如此

kubectl create -f xxx.yaml // 使用配置文件来创建资源
kubectl delete -f xxx.yaml // 使用配置文件来删除资源

kubectl apply -f xxx.yaml // 会把配置应用到集群中 感觉apply是包含了create和edit的能力

kubectl create service nginx-service
kubectl expose // 将资源公开为新的service 可以是将service deployment pod等创建为service
例如 kubectl expose deployment nginx-deployment
查看服务更加详细信息:
kubectl describe service nginx-deployment // 其他资源也可以用describe

在yaml修改spec: type: NodePort，即节点端口类型，然后在spec: ports: nodePort: [端口号]，端口号需要在30000-32767之间,否则会提示不正确

kubectl get all -n [命名空间] // 查看指定命名空间的资源 默认命名空间为default
```





