1、相关yaml文件：

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx-deploy
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx-deploy
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80

2、创建deployment
[root@fintest yaml]# kubectl apply -f nginx-dep01.yaml 
deployment.apps/nginx-deployment created
[root@fintest yaml]# 

3、查看deployment
[root@fintest ~]# kubectl get pod --show-labels
NAME                       READY   STATUS    RESTARTS   AGE     LABELS
my-nginx-c7dd45d78-ftwnp   1/1     Running   0          3h37m   app=my-nginx,pod-template-hash=c7dd45d78
my-nginx-c7dd45d78-zz65h   1/1     Running   0          22h     app=my-nginx,pod-template-hash=c7dd45d78
nginx-pod                  1/1     Running   0          26m     app=nginx-pod
[root@fintest ~]# kubectl get pod --show-labels
NAME                               READY   STATUS              RESTARTS   AGE     LABELS
my-nginx-c7dd45d78-ftwnp           1/1     Running             0          3h38m   app=my-nginx,pod-template-hash=c7dd45d78
my-nginx-c7dd45d78-zz65h           1/1     Running             0          22h     app=my-nginx,pod-template-hash=c7dd45d78
nginx-deployment-fc6884c76-gx9ck   0/1     ContainerCreating   0          6s      app=nginx-deploy,pod-template-hash=fc6884c76
nginx-deployment-fc6884c76-qxh57   1/1     Running             0          6s      app=nginx-deploy,pod-template-hash=fc6884c76
nginx-pod                          1/1     Running             0          27m     app=nginx-pod
[root@fintest ~]# kubectl get pod --show-labels
NAME                               READY   STATUS    RESTARTS   AGE     LABELS
my-nginx-c7dd45d78-ftwnp           1/1     Running   0          3h39m   app=my-nginx,pod-template-hash=c7dd45d78
my-nginx-c7dd45d78-zz65h           1/1     Running   0          22h     app=my-nginx,pod-template-hash=c7dd45d78
nginx-deployment-fc6884c76-gx9ck   1/1     Running   0          22s     app=nginx-deploy,pod-template-hash=fc6884c76
nginx-deployment-fc6884c76-qxh57   1/1     Running   0          22s     app=nginx-deploy,pod-template-hash=fc6884c76
nginx-pod                          1/1     Running   0          27m     app=nginx-pod
[root@fintest ~]# 

4、查看deployment详细

[root@fintest ~]# kubectl describe deployment nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Wed, 06 Jan 2021 17:55:43 +0800
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx-deploy
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-deploy
  Containers:
   nginx:
    Image:        nginx:alpine
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-fc6884c76 (2/2 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m12s  deployment-controller  Scaled up replica set nginx-deployment-fc6884c76 to 2
[root@fintest ~]# 

5、查看日志
[root@fintest ~]# kubectl logs -f nginx-deployment-fc6884c76-gx9ck 
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
172.18.175.61 - - [06/Jan/2021:10:01:03 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.61.1" "-"
172.18.175.61 - - [06/Jan/2021:10:01:05 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.61.1" "-"

6、缩容deployment

[root@fintest ~]# kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
my-nginx-c7dd45d78-ftwnp           1/1     Running   0          3h50m
my-nginx-c7dd45d78-zz65h           1/1     Running   0          22h
nginx-deployment-fc6884c76-69676   1/1     Running   0          3s
nginx-deployment-fc6884c76-xc6h8   1/1     Running   0          3s
[root@fintest ~]# kubectl scale --help
Set a new size for a Deployment, ReplicaSet, Replication Controller, or StatefulSet.

 Scale also allows users to specify one or more preconditions for the scale action.

 If --current-replicas or --resource-version is specified, it is validated before the scale is attempted, and it is
guaranteed that the precondition holds true when the scale is sent to the server.

Examples:
  # Scale a replicaset named 'foo' to 3.
  kubectl scale --replicas=3 rs/foo
  
  # Scale a resource identified by type and name specified in "foo.yaml" to 3.
  kubectl scale --replicas=3 -f foo.yaml
  
  # If the deployment named mysql's current size is 2, scale mysql to 3.
  kubectl scale --current-replicas=2 --replicas=3 deployment/mysql
  
  # Scale multiple replication controllers.
  kubectl scale --replicas=5 rc/foo rc/bar rc/baz
  
  # Scale statefulset named 'web' to 3.
  kubectl scale --replicas=3 statefulset/web

Options:
      --all=false: Select all resources in the namespace of the specified resource types
      --allow-missing-template-keys=true: If true, ignore any errors in templates when a field or map key is missing in
the template. Only applies to golang and jsonpath output formats.
      --current-replicas=-1: Precondition for current size. Requires that the current size of the resource match this
value in order to scale.
      --dry-run='none': Must be "none", "server", or "client". If client strategy, only print the object that would be
sent, without sending it. If server strategy, submit server-side request without persisting the resource.
  -f, --filename=[]: Filename, directory, or URL to files identifying the resource to set a new size
  -k, --kustomize='': Process the kustomization directory. This flag can't be used together with -f or -R.
  -o, --output='': Output format. One of:
json|yaml|name|go-template|go-template-file|template|templatefile|jsonpath|jsonpath-as-json|jsonpath-file.
      --record=false: Record current kubectl command in the resource annotation. If set to false, do not record the
command. If set to true, record the command. If not set, default to updating the existing annotation value only if one
already exists.
  -R, --recursive=false: Process the directory used in -f, --filename recursively. Useful when you want to manage
related manifests organized within the same directory.
      --replicas=0: The new desired number of replicas. Required.
      --resource-version='': Precondition for resource version. Requires that the current resource version match this
value in order to scale.
  -l, --selector='': Selector (label query) to filter on, supports '=', '==', and '!='.(e.g. -l key1=value1,key2=value2)
      --template='': Template string or path to template file to use when -o=go-template, -o=go-template-file. The
template format is golang templates [http://golang.org/pkg/text/template/#pkg-overview].
      --timeout=0s: The length of time to wait before giving up on a scale operation, zero means don't wait. Any other
values should contain a corresponding time unit (e.g. 1s, 2m, 3h).

Usage:
  kubectl scale [--resource-version=version] [--current-replicas=count] --replicas=COUNT (-f FILENAME | TYPE NAME)
[options]

Use "kubectl options" for a list of global command-line options (applies to all commands).
[root@fintest ~]# kubectl scale --replicas=1 deployment/nginx-deployment
deployment.apps/nginx-deployment scaled
[root@fintest ~]# kubectl get pods
NAME                               READY   STATUS        RESTARTS   AGE
my-nginx-c7dd45d78-ftwnp           1/1     Running       0          3h51m
my-nginx-c7dd45d78-zz65h           1/1     Running       0          22h
nginx-deployment-fc6884c76-69676   0/1     Terminating   0          72s
nginx-deployment-fc6884c76-xc6h8   1/1     Running       0          72s
[root@fintest ~]# kubectl get pods
NAME                               READY   STATUS        RESTARTS   AGE
my-nginx-c7dd45d78-ftwnp           1/1     Running       0          3h51m
my-nginx-c7dd45d78-zz65h           1/1     Running       0          22h
nginx-deployment-fc6884c76-69676   0/1     Terminating   0          74s
nginx-deployment-fc6884c76-xc6h8   1/1     Running       0          74s
[root@fintest ~]# kubectl get pods
NAME                               READY   STATUS        RESTARTS   AGE
my-nginx-c7dd45d78-ftwnp           1/1     Running       0          3h51m
my-nginx-c7dd45d78-zz65h           1/1     Running       0          22h
nginx-deployment-fc6884c76-69676   0/1     Terminating   0          75s
nginx-deployment-fc6884c76-xc6h8   1/1     Running       0          75s
[root@fintest ~]# kubectl get pods
NAME                               READY   STATUS        RESTARTS   AGE
my-nginx-c7dd45d78-ftwnp           1/1     Running       0          3h51m
my-nginx-c7dd45d78-zz65h           1/1     Running       0          22h
nginx-deployment-fc6884c76-69676   0/1     Terminating   0          79s
nginx-deployment-fc6884c76-xc6h8   1/1     Running       0          79s
[root@fintest ~]# kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
my-nginx-c7dd45d78-ftwnp           1/1     Running   0          3h52m
my-nginx-c7dd45d78-zz65h           1/1     Running   0          22h
nginx-deployment-fc6884c76-xc6h8   1/1     Running   0          88s
[root@fintest ~]# 

7、扩容deployment
[root@fintest ~]# kubectl scale --replicas=2 deployment/nginx-deployment
deployment.apps/nginx-deployment scaled
[root@fintest ~]# kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
my-nginx-c7dd45d78-ftwnp           1/1     Running   0          3h52m
my-nginx-c7dd45d78-zz65h           1/1     Running   0          22h
nginx-deployment-fc6884c76-xc6h8   1/1     Running   0          2m22s
nginx-deployment-fc6884c76-zkxqf   1/1     Running   0          2s
[root@fintest ~]# 

8、删除deployment

[root@fintest ~]# kubectl delete deploy nginx-deployment 
deployment.apps "nginx-deployment" deleted
[root@fintest ~]# kubectl get deploy
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   2/2     2            2           22h
[root@fintest ~]# kubectl get pods
NAME                       READY   STATUS    RESTARTS   AGE
my-nginx-c7dd45d78-ftwnp   1/1     Running   0          3h49m
my-nginx-c7dd45d78-zz65h   1/1     Running   0          22h
[root@fintest ~]# 

