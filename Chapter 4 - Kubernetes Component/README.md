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

```
$ kubectl describe pod (podname)
```

> *Replace (podname) with the existing node name without any brackets.*