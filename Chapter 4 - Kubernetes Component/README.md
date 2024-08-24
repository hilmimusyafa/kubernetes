## Chapter 4 : Basic of Kubernetes

### 4.1 Node

Node is a worker point in Kubernetes, previously called minion. Actually, Node is in the form of Virtual Machine or Physical Machine (Bare Metal), but there is additional management by Kubernetes.

Like the previous material, nodes already have kubelet, kube-proxy, and container-manager. You can see the picture below :

![Hos (5)](https://hackmd.io/_uploads/SywLZK4i0.png)

Well, as explained earlier, there is no need to know where Kubernetes places the node, because it is already the task of Kubernetes, only need to determine how many nodes are working on Kubernetes. If for example 2 nodes have been run, it turns out to be almost full or high traffic, then simply add a node without having to bother with manual settings.

To see the nodes that have been added to minukube Type the following command after starting Minikube :
```bash
$ kubectl get node
```
For details, just type the command:
```bash
$ kubectl describe node (nodename)
```
> *Replace (nodename) with the existing node name without any brackets.*

And then it will outputed like this  : 
```
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   47m   v1.30.0
```

### 4.2 Pod

#### 4.2.1 About Pod

Pod is the smallest unit that can be deployed in Kubernetes Cluster. Similar to deploying an application in Docker that is placed in a Container, the difference is deploying in Kubernetes will be deployed into a Pod, why is that? because in Kubernetes Pod can be filled with 1 or more containers. Yes, simply put, Pod contains applications that are run in Kubernetes Cluster.

The picture is like this :

![Hos.5png](https://hackmd.io/_uploads/BkaVLtNsC.png)

As seen in the picture in Pod can be 2 containers. Kubernetes uses a different layer called pod to run 2 or more containers directly.

How to view all Pods :

```bash
$ kubectl get pod
```

To view Pod details:

```bash
$ kubectl describe pod (podname)
```

> *Replace (podname) with the existing node name without any brackets.*

So, it will outputed like this : 
```
hilmi@minkube:~$ kubectl get pod
No resources found in default namespace.
```
Yeah, because i didn't make Pod it will outputed `No resources found in default namespace.`

#### 4.2.2 Making Pod

To create a pod, make sure you do it in the following steps:

1. Creating a Configuration File

   The first step to take to create a Pod is to use a yaml file, as below:
   
   4.2.2.1-template-pod.yaml
   ```yaml 
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-name
    spec:
      containers:
        - name: container-name
          image: image-name
          ports:
            - containerPort: 80
    ```
    With the explanation below:
    - **apiVersion: v1**; determines the pod API version used.
    - **kind: Pod**; determines the type to be created (of course Pod).
    - **metadata**; adds metadata for pods
    - **metadata: mame**; adds metadata to what the pod will be named.
    - **spec**; determines the configuration to be created.
    - **containers**; creates the containers themselves. (You can also create more than one in one configuration file).
    - **containers: name**; determines the name of the container.
    - **containers: image**; determines what image/application will be used in the container.
    - **ports: containerPort: 80**; determines the port that will run in the container.

    For example, here we will try with the nginx application, a lightweight web service application that runs on port 80.

    4.2.2.2-nginx-pod.yaml 
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-pod
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
    ```
    
2. Create a Pod with the .yaml file that has been created

    After creating the .yaml file, run it in the terminal with the command as below : 
    ```bash
    $ kubectl create -f (podfile).yaml
    ```
    > *Replace (podfile) with the existing node name without any brackets.*
    If you use a file created like the example, it will look like this : 
    ```bash
    $ kubectl create -f 4.2.2.2-nginx-pod.yaml 
    ```
    So it produces the output :
    ```
    pod/nginx created
    ```

3. Check the installed Pod

    Just like before to check the list of installed pods, use the command : 
    ```bash
    $ kubectl get pod
    ```
    So it will produce output : 
    ```
    NAME        READY   STATUS              RESTARTS   AGE
    nginx-pod   0/1     ContainerCreating   0          16s
    ```
    If you see, in that example Pod status is creating just wait and BOOM, like this : 
    ```
    NAME        READY   STATUS    RESTARTS   AGE
    nginx-pod   1/1     Running   0          15m 
    ```
    You can also get more details from the pod list with the command : 
    ```bash
    $ kubectl get pod -o wide
    ```
    So it will produce output : 
    ```
    NAME        READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
    nginx-pod   1/1     Running   0          23m   10.244.0.3   minikube   <none>           <none>
    ```
    And, for details of a single pod, you can type in the command : 
    ```
    $ kubectl describe pod (podname)
    ```
    > *Replace (podname) with the existing node name without any brackets.*

    And the example this : 
    ```
    $ kubectl describe pod 4.2.2.2-nginx-pod.yaml 
    ```

    And it will outputed like this : 
    ```
    Name:             nginx-pod
    Namespace:        default
    Priority:         0
    Service Account:  default
    Node:             minikube/192.168.49.2
    Start Time:       Fri, 23 Aug 2024 22:34:10 +0700
    Labels:           <none>
    Annotations:      <none>
    Status:           Running
    IP:               10.244.0.3
    IPs:
      IP:  10.244.0.3
    Containers:
      nginx:
        Container ID:   docker://20bc159abb465f1bf9bd4548805aa485091934463c6e365c24ec2ca972b88580
        Image:          nginx
        Image ID:       docker-pullable://nginx@sha256:447a8665cc1dab95b1ca778e162215839ccbb9189104c79d7ec3a81e14577add
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Fri, 23 Aug 2024 22:35:14 +0700
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jrw6d (ro)
    Conditions:
      Type                        Status
      PodReadyToStartContainers   True
      Initialized                 True
      Ready                       True
      ContainersReady             True
      PodScheduled                True
    Volumes:
      kube-api-access-jrw6d:
        Type:                    Projected (a volume that contains injected data from multiple sources)
        TokenExpirationSeconds:  3607
        ConfigMapName:           kube-root-ca.crt
        ConfigMapOptional:       <nil>
        DownwardAPI:             true
    QoS Class:                   BestEffort
    Node-Selectors:              <none>
    Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
    Events:
      Type    Reason     Age   From               Message
      ----    ------     ----  ----               -------
      Normal  Scheduled  25m   default-scheduler  Successfully assigned default/nginx-pod to minikube
      Normal  Pulling    25m   kubelet            Pulling image "nginx"
      Normal  Pulled     24m   kubelet            Successfully pulled image "nginx" in 1m0.809s (1m0.809s including waiting). Image size: 187694648 bytes.
      Normal  Created    24m   kubelet            Created container nginx
      Normal  Started    24m   kubelet            Started container nginx
    ```
    Yeah, its full of description and info that need it.
    
#### 4.2.3 Accessing Pod

In certain cases, it is necessary to check whether the pod is running properly or not. To check the Pod, use the access method with the command: 
   
```bash
$ kubectl port-forward (podname) (portaccess:podport)
```

> *Replace podname, portaccess, and podport with the existing node names without brackets. portaccess is the port that will be accessed through your device, podport is the port running in the Container according to the configuration*

For example, to access a previously created port : 
    
```bash
$ kubectl port-forward nginx-pod 8888:80
```
It will outputed like this : 
```
Forwarding from 127.0.0.1:8888 -> 80
Forwarding from [::1]:8888 -> 80
```
And if you check use cat command (`$ curl 127.0.0.1:8888`) it wil outputed nginx hello like this : 

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
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
```

> **
> 
#### 4.2.4 Deleting Pod

For certain cases, sometimes it is necessary to delete Pods, the way to delete Pods can use the following method:
    
```
$ kubectl delete pod (podname)
```
or 
```
$ kubectl delete pod (podname-1) (podname-2) (podname-3)
```
if there are many pods to delete, or
```
$ kubectl delete pod -l (key=value)
```
If based on a specific label key, and finally, if you want to delete what is based on namespace, you can use : 
```
$ kubectl delete pod -all --namespace (namespacename)
```   
    
Just adjust it for what kind of use.
    
### 4.3 Label

Labels in Kubernetes are useful for marking Pods, so that Pods will be organized, Pods will also be clearer because there is additional information in the Pod Label.

Labels are also not only for Pods, but all resources in Kubernetes, such as Replication Controllers, Replica Sets, Services, etc.

Here is an example of a Pod template with labels : 

4.3.1-template-pod-label.yaml 
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-name
  labels:
    label-key1: label-value1
    label-key2: label-value2
    label-key3: label-value3
spec:
  containers:
    - name: container-name
      image: image-name
      ports:
        - containerPort: 80
```
Actually it's the same, but the only difference is that there are additional labels, and here we will use nginx as before :

4.3.2-nginx-pod-labeled.yaml 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    team: finance
    version: 1.7.2
    environment: production
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
      - containerPort: 80
```
Here to write free labels as needed and as efficient as possible.

To insert Pods is the same as before:

```bash
$ kubectl create -f 4.3.2-nginx-pod-labeled.yaml 
```

> *Make sure the metadata name is different from the previously created Pod, or differentiate the Pod metadata name in the yaml so that it can be added*

To see previously created labels, use the command:
```bash
$ kubectl get pod --show-labels
```