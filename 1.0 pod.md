1、POD的相关yaml文件：
yaml file:

apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.19.6-alpine
    ports:
    - containerPort: 80
	
2、创建pod:
  
 [root@fintest yaml]# kubectl apply -f nginx-pod.yaml 
pod/nginx-pod created

3、查看POD：
[root@fintest ~]#  kubectl get pods nginx-pod -owide
NAME        READY   STATUS    RESTARTS   AGE   IP              NODE      NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          43s   192.168.24.70   fintest   <none>           <none>
[root@fintest ~]# 

4、查看pod的详细：

[root@fintest ~]# kubectl describe pod nginx-pod
Name:         nginx-pod
Namespace:    default
Priority:     0
Node:         fintest/172.18.175.61
Start Time:   Wed, 06 Jan 2021 17:28:11 +0800
Labels:       app=nginx-pod
Annotations:  cni.projectcalico.org/podIP: 192.168.24.70/32
              cni.projectcalico.org/podIPs: 192.168.24.70/32
Status:       Running
IP:           192.168.24.70
IPs:
  IP:  192.168.24.70
Containers:
  nginx:
    Container ID:   docker://2246e01250f76b8f5e5c5af0399129de16194bc06ff6ef8ede03f37cc4cb1f80
    Image:          nginx:1.19.6-alpine
    Image ID:       docker-pullable://nginx@sha256:c2ce58e024275728b00a554ac25628af25c54782865b3487b11c21cafb7fabda
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 06 Jan 2021 17:28:12 +0800
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-g8k96 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-g8k96:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-g8k96
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  16m   default-scheduler  Successfully assigned default/nginx-pod to fintest
  Normal  Pulled     15m   kubelet            Container image "nginx:1.19.6-alpine" already present on machine
  Normal  Created    15m   kubelet            Created container nginx
  Normal  Started    15m   kubelet            Started container nginx
[root@fintest ~]# 


5、进入Pod，执行命令：
[root@fintest ~]# kubectl exec -it nginx-pod -- sh
/ # ps -ef
PID   USER     TIME  COMMAND
    1 root      0:00 nginx: master process nginx -g daemon off;
   30 nginx     0:00 nginx: worker process
   31 nginx     0:00 nginx: worker process
   32 root      0:00 sh
   38 root      0:00 ps -ef
/ # netstat -lntp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1/nginx: master pro
tcp        0      0 :::80                   :::*                    LISTEN      1/nginx: master pro
/ # 

6、检查pod提供的服务：
[root@fintest ~]# kubectl get pod -owide
NAME                       READY   STATUS    RESTARTS   AGE     IP              NODE      NOMINATED NODE   READINESS GATES
my-nginx-c7dd45d78-ftwnp   1/1     Running   0          3h31m   192.168.24.69   fintest   <none>           <none>
my-nginx-c7dd45d78-zz65h   1/1     Running   0          22h     192.168.24.68   fintest   <none>           <none>
nginx-pod                  1/1     Running   0          19m     192.168.24.70   fintest   <none>           <none>
[root@fintest ~]# curl http://192.168.24.70
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

7、删除pod

[root@fintest yaml]# kubectl delete -f nginx-pod.yaml 
pod "nginx-pod" deleted
[root@fintest yaml]# 

[root@fintest ~]# kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
my-nginx-c7dd45d78-ftwnp           1/1     Running   0          3h46m
my-nginx-c7dd45d78-zz65h           1/1     Running   0          22h
nginx-deployment-fc6884c76-gx9ck   1/1     Running   0          7m39s
nginx-deployment-fc6884c76-qxh57   1/1     Running   0          7m39s
[root@fintest ~]# 



