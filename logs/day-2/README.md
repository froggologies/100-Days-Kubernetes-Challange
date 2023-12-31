# DAY 2

![Static Badge](https://img.shields.io/badge/Date-31--7--2023-f5f5f5?logo=googlecalendar&logoColor=f5f5f5)
![Static Badge](https://img.shields.io/badge/Docker-v24.0.2-2496ed?logo=docker&logoColor=2496ed)
![Static Badge](https://img.shields.io/badge/minikube-v1.30.1-326ce5?logo=kubernetes&logoColor=326ce5)

## Deploy an app

### 1. Deploy an app

To deploy an app to our cluster, we must create a **Kubernetes Deployment**. In this command, I use the NodeJS app sample image stored in Google Cloud Container Registry (GCR).

`kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1`

> A Deployment is responsible for creating and updating instances of your application

Run this command to see our deployment.

`kubectl get deployments`

output:
```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           30m
```

We can also see our pods are ready.

`kubectl get pods`

output:
```
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-5485cc6795-vckkb   1/1     Running   0          5m15s
```

### 2. View the app

Basically, when we use kubectl command, we are interacting through Kubernetes API.

But we can expose that API by using a proxy.

`kubectl proxy`

Then we can access that API from the local network on port 8001. 

Output the Kubernetes API version.

`curl http://localhost:8001/version`

output:
```
{
  "major": "1",
  "minor": "26",
  "gitVersion": "v1.26.3",
  "gitCommit": "9e644106593f3f4aa98f8a84b23db5fa378900bd",
  "gitTreeState": "clean",
  "buildDate": "2023-03-15T13:33:12Z",
  "goVersion": "go1.19.7",
  "compiler": "gc",
  "platform": "linux/arm64"
}%
```

Save the pod name that we created before into the environment variable so that we can reference it in another command later.

`export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')`

`echo Name of the Pod: $POD_NAME`

In here we try to fetch the pod info:

`curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/`

output:
```
{
  "kind": "Pod",
  "apiVersion": "v1",
  "metadata": {
    .
    .
    .
  }
}
```

## Viewing Pods and Nodes

### 1. Pods

**Pods** are the tiniest component in the Kubernetes framework, generated by **Deployment**. Within a pod, there exists one or more containers that are closely interconnected, enabling them to share IP addresses or volumes. When a **Node** encounters a failure, identical pods are promptly spawned on another Node.

### 2. Nodes

**Nodes** can either be virtual machines or physical machines. These Nodes are under the management of the **Control Plane**. Each Node is equipped with a kubelet, which is in charge of executing commands received from the Control Plane. Additionally, Nodes require a container runtime, such as *containerd* or *Docker*.

### 3. Troubleshooting with kubectl

- `kubectl get` - list resources
- `kubectl describe` - show detailed information about a resource
- `kubectl logs` - print the logs from a container in a pod
- `kubectl exec` - execute a command on a container in a pod

Subcommand **get**, is used to get a list of any resources such as pods, services, deployments, etc. Here we view the list of pods that exist in our cluster.

`kubectl get pods`

```
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-5485cc6795-vckkb   1/1     Running   0          9h
```

**describe** is used to get detailed information about our K8s cluster resources.

`kubectl describe pods`

```
Name:             kubernetes-bootcamp-5485cc6795-vckkb
Namespace:        default
Priority:         0
Service Account:  default
                  .
                  .
                  .
```

Usually, the application sends standard output that becomes **logs** for the container, this logs we can view it using this command.

`kubectl logs "$POD_NAME"`

```
Kubernetes Bootcamp App Started At: 2023-07-31T07:01:56.053Z | Running On:  kubernetes-bootcamp-5485cc6795-vckkb 
```

> If there is only one container inside the pod, we don't need to specify the container name.

**exec** is used to execute a command inside a container in a pod. Here we try to output the environment variables in this container.

`kubectl exec "$POD_NAME" -- env`

```
HOME=/root
NODE_VERSION=6.3.1
NPM_CONFIG_LOGLEVEL=info
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT=tcp://10.96.0.1:443
HOSTNAME=kubernetes-bootcamp-5485cc6795-vckkb
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

We can also use this command to run bash interactively inside it using `-ti` flags.

`kubectl exec -ti $POD_NAME -- bash`

In this NodeJS sample app, there is a server.js that we can view inside it.

`cat server.js`

This server.js is open on port 8080 and response with simple console.log.

`curl http://localhost:8080`

```
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-5485cc6795-vckkb | v=1
```

We use `exit` to close this interactive bash.

`exit`

## Navigation

[`◀︎ PREVIOUS`](../day-1/README.md) ∙ [ `HOME` ](../../README.md) ∙ [`NEXT ▶︎`](../day-3/README.md)
## Reference
- [Using kubectl to Create a Deployment](https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-intro/)
- [Viewing Pods and Nodes](https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/)