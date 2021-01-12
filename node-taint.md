root@10.0.100.233--> mo]#cp pod-affinity-demo.yaml node-taint-demo.yaml
root@10.0.100.233--> mo]#vi node-taint-demo.yaml 
root@10.0.100.233--> mo]#
root@10.0.100.233--> mo]#
root@10.0.100.233--> mo]#
root@10.0.100.233--> mo]#cat node-taint-demo.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-taint
  labels:
    app: test-taint
spec:
  replicas: 3
  revisionHistoryLimit: 15
  selector:
    matchLabels:
      app: test-taint
      role: test
  template:
    metadata:
      labels:
        app: test-taint
        role: test
    spec:
      containers:
      - name: nginx
        image: 10.0.100.240:5050/nginx:1.13.2
        ports:
        - containerPort: 80
          name: nginxweb-t
      tolerations:
      - effect: NoSchedule
        key: testtaint
        operator: Equal
        value: cry
root@10.0.100.233--> mo]#kubectl apply -f node-taint-demo.yaml 
deployment.apps/test-taint created
root@10.0.100.233--> mo]#kubectl get pod
NAME                          READY   STATUS    RESTARTS   AGE
my-nginx-7586cd6bf7-77hfh     1/1     Running   0          83m
my-nginx-7586cd6bf7-r4xp6     1/1     Running   0          83m
myapp01                       1/1     Running   0          13d
nginx-ds-55ffq                1/1     Running   1          55d
nginx-ds-9mskg                1/1     Running   1          55d
nginx-ds-nxc27                1/1     Running   1          55d
test-taint-6c99dff984-n2mp8   1/1     Running   0          5s
test-taint-6c99dff984-nkxwn   1/1     Running   0          5s
test-taint-6c99dff984-r2r95   1/1     Running   0          5s
root@10.0.100.233--> mo]#kubectl get pod -o wide
NAME                          READY   STATUS    RESTARTS   AGE   IP             NODE         NOMINATED NODE   READINESS GATES
my-nginx-7586cd6bf7-77hfh     1/1     Running   0          84m   172.30.144.6   mojl-k8s01   <none>           <none>
my-nginx-7586cd6bf7-r4xp6     1/1     Running   0          84m   172.30.48.2    mojl-k8s03   <none>           <none>
myapp01                       1/1     Running   0          13d   172.30.48.5    mojl-k8s03   <none>           <none>
nginx-ds-55ffq                1/1     Running   1          55d   172.30.48.3    mojl-k8s03   <none>           <none>
nginx-ds-9mskg                1/1     Running   1          55d   172.30.144.2   mojl-k8s01   <none>           <none>
nginx-ds-nxc27                1/1     Running   1          55d   172.30.104.2   mojl-k8s02   <none>           <none>
test-taint-6c99dff984-n2mp8   1/1     Running   0          11s   172.30.144.7   mojl-k8s01   <none>           <none>
test-taint-6c99dff984-nkxwn   1/1     Running   0          11s   172.30.48.6    mojl-k8s03   <none>           <none>
test-taint-6c99dff984-r2r95   1/1     Running   0          11s   172.30.104.4   mojl-k8s02   <none>           <none>
root@10.0.100.233--> mo]#vi node-taint-demo.yaml 
root@10.0.100.233--> mo]#cat node-taint-demo.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-taint
  labels:
    app: test-taint
spec:
  replicas: 3
  revisionHistoryLimit: 15
  selector:
    matchLabels:
      app: test-taint
      role: test
  template:
    metadata:
      labels:
        app: test-taint
        role: test
    spec:
      containers:
      - name: nginx
        image: 10.0.100.240:5050/nginx:1.13.2
        ports:
        - containerPort: 80
          name: nginxweb-t
#      tolerations:
#      - effect: NoSchedule
#        key: testtaint
#        operator: Equal
#        value: cry
root@10.0.100.233--> mo]#kubectl apply -f node-taint-demo.yaml 
deployment.apps/test-taint configured
root@10.0.100.233--> mo]#kubectl get pod -o wide
NAME                          READY   STATUS        RESTARTS   AGE   IP             NODE         NOMINATED NODE   READINESS GATES
my-nginx-7586cd6bf7-77hfh     1/1     Running       0          85m   172.30.144.6   mojl-k8s01   <none>           <none>
my-nginx-7586cd6bf7-r4xp6     1/1     Running       0          85m   172.30.48.2    mojl-k8s03   <none>           <none>
myapp01                       1/1     Running       0          13d   172.30.48.5    mojl-k8s03   <none>           <none>
nginx-ds-55ffq                1/1     Running       1          55d   172.30.48.3    mojl-k8s03   <none>           <none>
nginx-ds-9mskg                1/1     Running       1          55d   172.30.144.2   mojl-k8s01   <none>           <none>
nginx-ds-nxc27                1/1     Running       1          55d   172.30.104.2   mojl-k8s02   <none>           <none>
test-taint-6c99dff984-n2mp8   0/1     Terminating   0          84s   172.30.144.7   mojl-k8s01   <none>           <none>
test-taint-6c99dff984-nkxwn   0/1     Terminating   0          84s   172.30.48.6    mojl-k8s03   <none>           <none>
test-taint-6c99dff984-r2r95   0/1     Terminating   0          84s   172.30.104.4   mojl-k8s02   <none>           <none>
test-taint-848b54d459-lgnrf   1/1     Running       0          6s    172.30.104.9   mojl-k8s02   <none>           <none>
test-taint-848b54d459-p86hq   1/1     Running       0          4s    172.30.48.8    mojl-k8s03   <none>           <none>
test-taint-848b54d459-xnnsz   1/1     Running       0          5s    172.30.48.7    mojl-k8s03   <none>           <none>
root@10.0.100.233--> mo]#kubectl get pod -o wide
NAME                          READY   STATUS    RESTARTS   AGE   IP             NODE         NOMINATED NODE   READINESS GATES
my-nginx-7586cd6bf7-77hfh     1/1     Running   0          85m   172.30.144.6   mojl-k8s01   <none>           <none>
my-nginx-7586cd6bf7-r4xp6     1/1     Running   0          85m   172.30.48.2    mojl-k8s03   <none>           <none>
myapp01                       1/1     Running   0          13d   172.30.48.5    mojl-k8s03   <none>           <none>
nginx-ds-55ffq                1/1     Running   1          55d   172.30.48.3    mojl-k8s03   <none>           <none>
nginx-ds-9mskg                1/1     Running   1          55d   172.30.144.2   mojl-k8s01   <none>           <none>
nginx-ds-nxc27                1/1     Running   1          55d   172.30.104.2   mojl-k8s02   <none>           <none>
test-taint-848b54d459-lgnrf   1/1     Running   0          14s   172.30.104.9   mojl-k8s02   <none>           <none>
test-taint-848b54d459-p86hq   1/1     Running   0          12s   172.30.48.8    mojl-k8s03   <none>           <none>
test-taint-848b54d459-xnnsz   1/1     Running   0          13s   172.30.48.7    mojl-k8s03   <none>           <none>
root@10.0.100.233--> mo]#kubectl kubectl delete -f node-taint-demo.yaml 
Error: unknown command "kubectl" for "kubectl"
Run 'kubectl --help' for usage.
unknown command "kubectl" for "kubectl"
root@10.0.100.233--> mo]#kubectl delete -f node-taint-demo.yaml 
deployment.apps "test-taint" deleted
root@10.0.100.233--> mo]#vi node-taint-demo.yaml 
root@10.0.100.233--> mo]#kubectl apply -f node-taint-demo.yaml 
deployment.apps/test-taint created
root@10.0.100.233--> mo]#kubectl get pod -owide
NAME                         READY   STATUS    RESTARTS   AGE    IP             NODE         NOMINATED NODE   READINESS GATES
my-nginx-7586cd6bf7-77hfh    1/1     Running   0          106m   172.30.144.6   mojl-k8s01   <none>           <none>
my-nginx-7586cd6bf7-r4xp6    1/1     Running   0          106m   172.30.48.2    mojl-k8s03   <none>           <none>
myapp01                      1/1     Running   0          13d    172.30.48.5    mojl-k8s03   <none>           <none>
nginx-ds-55ffq               1/1     Running   1          55d    172.30.48.3    mojl-k8s03   <none>           <none>
nginx-ds-9mskg               1/1     Running   1          55d    172.30.144.2   mojl-k8s01   <none>           <none>
nginx-ds-nxc27               1/1     Running   1          55d    172.30.104.2   mojl-k8s02   <none>           <none>
test-taint-f88c5c8fd-fq85g   1/1     Running   0          9s     172.30.144.7   mojl-k8s01   <none>           <none>
test-taint-f88c5c8fd-rhj4q   1/1     Running   0          9s     172.30.104.4   mojl-k8s02   <none>           <none>
test-taint-f88c5c8fd-srztx   1/1     Running   0          9s     172.30.48.6    mojl-k8s03   <none>           <none>
root@10.0.100.233--> mo]#kubectl delete -f node-taint-demo.yaml 
deployment.apps "test-taint" deleted
root@10.0.100.233--> mo]#cat node-taint-demo.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-taint
  labels:
    app: test-taint
spec:
  replicas: 3
  revisionHistoryLimit: 15
  selector:
    matchLabels:
      app: test-taint
      role: test
  template:
    metadata:
      labels:
        app: test-taint
        role: test
    spec:
      containers:
      - name: nginx
        image: 10.0.100.240:5050/nginx:1.13.2
        ports:
        - containerPort: 80
          name: nginxweb-t
      tolerations:
      - effect: NoSchedule
        key: testtaint
        operator: Exists
        #value: cry
root@10.0.100.233--> mo]#
