class: center, middle

# Introduction to Kubernetes

---

class: center, middle

<iframe width="560" height="315" src="https://www.youtube.com/embed/4ht22ReBjno" frameborder="0" allowfullscreen></iframe>

---

class: middle
background-image: url(https://i.kinja-img.com/gawker-media/image/upload/gf2yaqbwgtfeodvlyiga.jpg)
layout: true

---

.container-fluid[
## Kubernetes

* Open-source platform for automating deployment, scaling, and operations of application containers across clusters of hosts, providing container-centric infrastructure.
* The project was started by Google in 2014.
* Kubernetes **is not** a traditional, all-inclusive PaaS (Platform as a Service) system.
* Kubernetes is a framework for building distributed systems.
]

---

.container-fluid[
## Kubernetes

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">The Kubernetes project is hitting phase two; that&#39;s where the distros come in. Take your pick from Dies, OpenShift, Tectonic, GKE, and more.</p>&mdash; Kelsey Hightower (@kelseyhightower) <a href="https://twitter.com/kelseyhightower/status/775494220278738944">September 13, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
]

---

background-image: url(http://www.inquiriesjournal.com/article-images/uid-2647-1425259824/0740e1.jpg)

.container-fluid[
## Kubernetes

* Greek for _Helmsman_; also the root of the words _governor_ and _cybernetic_.
* **K8s** is an abbreviation derived by replacing the 8 letters _ubernete_ with 8.
]

---

class: middle
background-image: url(https://s-media-cache-ak0.pinimg.com/originals/a8/2e/2c/a82e2c9eda26ef4183a163007153c552.jpg)
layout: true

---

.container-fluid[
## Architecture

### The Kubernetes Node

#### `kubelet`

* The `kubelet` manages pods and their containers, their images, their volumes, etc.

#### `kube-proxy`

* A simple network proxy and load balancer.
* Can do simple TCP and UDP stream forwarding (round robin) across a set of backends.
]

---

.container-fluid[
## Architecture

### The Kubernetes Control Plane

#### `etcd`

* Distributed reliable key-value store.

#### Kubernetes API Server

* Intended to be a CRUD-y server, with most/all business logic implemented in separate components or in plug-ins.
* Mainly processes REST operations, validates them, and updates the corresponding objects in `etcd` (and eventually other stores).

#### Scheduler

* Binds unscheduled pods to nodes.

#### Kubernetes Controller Manager Server

* All other cluster-level functions are currently performed by the Controller Manager.
]

---

class: middle
background-image: url(http://www.coupofy.com/m/Store/content/ByUsf8fw.jpg)
layout: true

---

.container-fluid[
## Pods

.row[
.col-sm-6[
* A group of Docker containers with shared namespaces and shared volumes.
* Containers witih a pod share an IP address and port space, and can find each other via `localhost`.
]
.col-sm-6[
```
apiVersion: v1
kind: Pod
metadata:
spec:
  containers:
    image: nginx:1.7.9
    ports:
    - containerPort: 80
```
]
]
]

---

background-image: url(http://images.nationalgeographic.com/wpf/media-live/photos/000/029/overrides/crypt-rome-italy_2947_990x742.jpg)

.container-fluid[
## Pods

> Kubernetes Pods are mortal. They are born they die, and they are not resurrrected.
]

---

class: middle
background-image: url(http://resource2.ultdb.net/res/org0011/HSS/14-04/201403311SAC.jpg)
layout: true

---


.container-fluid[
## Labels

.row[
.col-sm-6[
* Key/value pairs that are attached to objects, such as pods.
* Are intended to be used to specify identifying attributes of objects.
* Can be used to organize and to select subsets of objects.
* Can be attached to objects at creation time and subsequently added and modified at any time.
]
.col-sm-6[
```
metadata:
  labels:
    release: stable
    environment: dev
    tier: frontend
    partition: customerA
    track: daily
```
]
]
]

---


.container-fluid[
## Annotations

.row[
.col-sm-6[
* Not used to identify and select objects.
* The metadata in an annotation can be small or large, structured or unstructured, and can include characters not permitted by labels.
]
.col-sm-6[
```
metadata:
  annotations:
    key1: value1
    key2: value2
```
]
]
]

---

class: middle
background-image: url(https://upload.wikimedia.org/wikipedia/commons/c/c2/CN_Box_Car_Loader.JPG)
layout: true

---

.container-fluid[
## Label Selectors

* Via a *label selector*, the client/user can identify a set of objects.
* The API currently supports two types of selectors: *equality-based* and *set-based*.
* An empty label selector (that is, one with zero requirements) selects every object in the collection.
* A null label selector (which is only possible for optional selector fields) selects no objects.
]

---

.container-fluid[
## Label Selectors

### *Equality-based* requirements 

```
selector:
    component: redis
```

### *Set-based* requirements


```
selector:
  matchLabels:
    component: redis
  matchExpressions:
    - {key: tier, operator: In, values: [cache]}
    - {key: environment, operator: NotIn, values: [dev]}
```
]

---

class: middle
background-image: url(http://i2.wp.com/nerdbastards.com/wp-content/uploads/2014/12/Rick-Deckard.jpg)
layout: true

---

.container-fluid[
## Replication Controllers

.row[
.col-sm-6[
* Ensures that a specified number of pod *replicas* are running at any one time.
* If there are too many pods, it will kill some.
* If there are too few, the replication controller will start more.
* The pods maintained by a replication controller are automatically replaced if they fail, get deleted, or are terminated.
]
.col-sm-6[
```
apiVersion: v1
kind: ReplicationController
metadata:
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        image: nginx
        ports:
        - containerPort: 80
```
]
]
]

---

class: middle
background-image: url(https://didyouseethatone.files.wordpress.com/2014/10/blade-runner-3.jpg)
layout: true

---

.container-fluid[
## Replica Sets

* The next-generation Replication Controller.
* Replica Set supports the new set-based selector requirements.
* Can also be a target for Horizontal Pod Autoscalers (HPA).
]

---

.container-fluid[
## Replica Sets

```
apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
spec:
  replicas: 3
  selector:
    matchExpressions:
    - {key: app, operator: In, values: [nginx]}
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        image: nginx
        ports:
        - containerPort: 80
```
]

---

class: middle
background-image: url(http://images.nationalgeographic.com/wpf/media-live/photos/000/829/cache/army-deployment-plane-military_82981_990x742.jpg)
layout: true

---

.container-fluid[
## Deployments

* Provides declarative updates for Pods and Replica Sets (the next-generation Replication Controller).
* A typical use case is:
  * Create a Deployment to bring up a Replica Set and Pods.
  * Check the status of a Deployment to see if it succeeds or not.
  * Later, update that Deployment to recreate the Pods (for example, to use a new image).
  * Rollback to an earlier Deployment revision if the current Deployment isn’t stable.
  * Pause and resume a Deployment.
]

---

.container-fluid[
## Deployments

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```
]

---

class: middle
background-image: url(http://www.hotel-r.net/im/hotel/az/hotel-service-4.jpg)
layout: true

---

.container-fluid[
## Services

* A Kubernetes `Service` is an abstraction which defines a logical set of `Pod`s and a policy by which to access them - sometimes called **a micro-service**. 
* The set of `Pod`s targeted by a `Service` is (usually) determined by a Label Selector.
* `kube-proxy` is responsible for implementing a form of virtual IP for `Service`s other than `ExternalName`.
* Kubernetes supports multiple port definitions on a `Service` object.
* Kubernetes supports 2 primary modes of finding a `Service`: 
  * environment variables (`MY_NGINX_SERVICE_HOST=10.0.162.149`, `MY_NGINX_SERVICE_PORT=80`),
  * DNS (`my-nginx.default.svc.cluster.local`).
]

---

.container-fluid[
## Services

```
apiVersion: v1
kind: Service
metadata:
spec:
  ports:
    - port: 8765
      targetPort: 9376
  selector:
    app: example
```
]


---

.container-fluid[
## Services

* `ClusterIP`
  * Use a cluster-internal IP only.
  * Service to be reachable only from inside of the cluster.
* `NodePort`
  * On top of having a cluster-internal IP.
  * Expose the service on a port on each node of the cluster (the same port on each node).
* `LoadBalancer`
  * On top of having a cluster-internal IP and exposing service on a `NodePort` also, ask the cloud provider for a load balancer.
* `ExternalName`
  * Map the service to the contents of the `externalName` field (e.g. `foo.bar.example.com`), by returning a `CNAME` record with its value.
  * No proxying of any kind is set up. 
]

---

class: middle
background-image: url(http://1.bp.blogspot.com/-xihe-4kodOw/VWB3EdjWDuI/AAAAAAAABqc/AzrfX3mW5YY/s1600/proel%2Bmixer.jpg)
layout: true

---

.container-fluid[
## Volumes

* A volume is just a directory, possibly with some data in it, which is accessible to the containers in a pod.
* Has an explicit lifetime - the same as the pod that encloses it.
* Kubernetes supports many type of volumes, and a Pod can use any number of them simultaneously.
]

---

.container-fluid[
## Volumes
.row[
.col-sm-6[
* `emptyDir`
* `hostPath`
* `gcePersistentDisk`
* `awsElasticBlockStore`
* `nfs`
* `iscsi`
* `flocker`
* `glusterfs`
* `rbd`
]
.col-sm-6[
* `cephfs`
* `gitRepo`
* `secret`
* `persistentVolumeClaim`
* `downwardAPI`
* `azureFileVolume`
* `azureDisk`
* `vsphereVolume`
* `Quobyte`
]
]
]

---

.container-fluid[
## Volumes

```
apiVersion: v1
kind: Pod
metadata:
spec:
  containers:
    image: redis
    volumeMounts:
      mountPath: /data/redis
  volumes:
    emptyDir: {}
```
]

---

class: middle
background-image: url(http://previews.123rf.com/images/dimol/dimol1206/dimol120600066/13993304-Old-vintage-retro-golden-compass-on-ancient-map-Stock-Photo.jpg)
layout: true

---

.container-fluid[
## Config Maps

.row[
.col-sm-6[
* The `ConfigMap` API resource holds key-value pairs of configuration data.
* Can be consumed in pods or used to store configuration data for system components such as controllers. 
* `ConfigMap` is similar to Secrets, but designed to more conveniently support working with strings that do not contain sensitive information.
]
.col-sm-6[
```
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: default
data:
  special.how: very
  special.type: charm
```
]
]
]

---

.container-fluid[
## Config Maps

ConfigMaps can be used to:
1. Populate the value of environment variables.
2. Set command-line arguments in a container.
3. Populate config files in a volume.
]

---

.container-fluid[
## Config Maps

### Consume `ConfigMap` in environment variables

```
containers:
    image: gcr.io/google_containers/busybox
    command: [ "/bin/sh", "-c", "env" ]
    env:
        valueFrom:
          configMapKeyRef:
            key: special.how
        valueFrom:
          configMapKeyRef:
            key: special.type
```
]

---

.container-fluid[
## Config Maps

### Set command-line arguments with `ConfigMap`

```
containers:
    image: gcr.io/google_containers/busybox
    command: [ "/bin/sh", "-c", "echo $(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)" ]
    env:
        valueFrom:
          configMapKeyRef:
            key: special.how
        valueFrom:
          configMapKeyRef:
            key: special.type
```
]

---

.container-fluid[
## Config Maps

### Consume `ConfigMap` via volume plugin


```
containers:
    image: gcr.io/google_containers/busybox
    command: [ "/bin/sh", "-c", "cat /etc/config/special.how" ]
    volumeMounts:
      mountPath: /etc/config
volumes:
    configMap:
```
]

---

class: middle
background-image: url(http://blog.marketo.com/wp-content/uploads/2013/09/telling-secrets-big-e1379620235254.jpg)
layout: true

---

.container-fluid[
## Secrets

.row[
.col-sm-6[
* Are intended to hold sensitive information, such as passwords, OAuth tokens, and SSH keys
* Kubernetes automatically creates secrets which contain credentials for accessing the API and it automatically modifies your pods to use this type of secret.
* Can be mounted as data volumes or be exposed as environment variables to be used by a container in a pod.
]
.col-sm-6[
```
apiVersion: v1
kind: Secret
metadata:
type: Opaque
data:
  password: MWYyZDFlMmU2N2Rm
```
]
]
]

---

.container-fluid[
## Secrets

### Using Secrets as Environment Variables

```
containers:
    image: redis
    env:
        valueFrom:
          secretKeyRef:
            key: username
        valueFrom:
          secretKeyRef:
            key: password
```
]

---

class: middle
background-image: url(http://photos1.blogger.com/blogger/6915/2218/1600/lost%20three%20minutes%20station%20of%20the%20window.jpg)
layout: true

---

.container-fluid[
## Others

* `DaemonSet`
* `HorizontalPodAutoscaler`
* `Job`
* `NetworkPolicy`
* `PersistentVolume`
* `PersistentVolumeClaim`
* `PetSet`
* `ServiceAccount`
* `ThirdPartyResource`
* ...
]

---

layout: true

## Minikube

---

```
$ brew update
Already up-to-date.
```

---

```
$ brew cask install virtualbox
==> Downloading http://download.virtualbox.org/virtualbox/5.1.6/VirtualBox-5.1.6-110634-OSX.dmg
Already downloaded: /Users/mstrzele/Library/Caches/Homebrew/Cask/virtualbox--5.1.6-110634.dmg
==> Verifying checksum for Cask virtualbox
==> Running installer for virtualbox; your password may be necessary.
==> Package installers may write to any location; options such as --appdir are ignored.
==> installer: Package name is Oracle VM VirtualBox
==> installer: Upgrading at base path /
==> installer: The upgrade was successful.
🍺  virtualbox was successfully installed!
```

---

```
$ brew cask install minikube
==> Satisfying dependencies
complete
==> Downloading https://github.com/kubernetes/minikube/releases/download/v0.11.0/minikube-darwin-amd64
Already downloaded: /Users/mstrzele/Library/Caches/Homebrew/Cask/minikube--0.11.0
==> Verifying checksum for Cask minikube
==> Symlinking Binary 'minikube-darwin-amd64' to '/usr/local/bin/minikube'
🍺  minikube was successfully installed!
```

---

```
$ brew install kubectl
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-cli-1.3.7.sierra.bottle.tar.gz
Already downloaded: /Users/mstrzele/Library/Caches/Homebrew/kubernetes-cli-1.3.7.sierra.bottle.tar.gz
==> Pouring kubernetes-cli-1.3.7.sierra.bottle.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-cli/1.3.7: 6 files, 42.9M

```

---

```
$ minikube start
Starting local Kubernetes cluster...
Kubectl is now configured to use the cluster.
```

--

```
$ minikube status
minikubeVM: Running
localkube: Running
```

--

```
$ kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443
kubernetes-dashboard is running at https://192.168.99.100:8443/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

---

layout: true

## Kubectl

---

```
$ source <(kubectl completion bash)
```

---

```
$ kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /Users/mstrzele/.minikube/ca.crt
    server: https://192.168.99.100:8443
contexts:
- context:
    cluster: minikube
    user: minikube
current-context: minikube
kind: Config
preferences: {}
users:
  user:
    client-certificate: /Users/mstrzele/.minikube/apiserver.crt
    client-key: /Users/mstrzele/.minikube/apiserver.key
```

---

layout: true

## Pods

---

```
$ kubectl explain pods
DESCRIPTION:
Pod is a collection of containers that can run on a host. This resource is created by clients and scheduled onto hosts.

FIELDS:
   spec  <Object>
     Specification of the desired behavior of the pod. More info:
     http://releases.k8s.io/release-1.4/docs/devel/api-conventions.md#spec-and-status

   status  <Object>
     Most recently observed status of the pod. This data may not be up to date.
     Populated by the system. Read-only. More info:
     http://releases.k8s.io/release-1.4/docs/devel/api-conventions.md#spec-and-status

```

---

```
apiVersion: v1
kind: Pod
metadata:
spec:
  restartPolicy: Never
  containers:
    image: "ubuntu:14.04"
    command: ["/bin/echo", "hello", "world"]
```

`wget https://mstrzele.github.io/intro-to-k8s/hello-world-pod.yaml`

---

```
$ kubectl create -f hello-world-pod.yaml
pod "hello-world" created
```

--

```
$ kubectl get pods
NAME          READY     STATUS              RESTARTS   AGE
hello-world   0/1       ContainerCreating   0          4s
```

--

```
$ kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
  info: 1 completed object(s) was(were) not shown in pods list. Pass --show-all to see all objects.

```

---

```
$ kubectl get pods --show-all
NAME          READY     STATUS      RESTARTS   AGE
hello-world   0/1       Completed   0          2m
```

--

```
$ kubectl logs hello-world
hello world
```

--

```
$ kubectl describe pod hello-world
```

---

```
$ kubectl create -f hello-world-pod.yaml
Error from server: error when creating "hello-world-pod.yaml": pods "hello-world" already exist
```

--

```
$ kubectl delete pod hello-world
pod "hello-world" deleted
```

--

```
$ kubectl create -f hello-world-pod.yaml
pod "hello-world" created
```

--

```
$ kubectl delete -f hello-world-pod.yaml
pod "hello-world" deleted
```

---

layout: true

## Deployments

---

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: hello
    spec:
      containers:
        image: mstrzele/hello-php:v1
        ports:
        - containerPort: 8000
        image: mstrzele/hello-node:v1
        ports:
        - containerPort: 8080
```

`wget https://mstrzele.github.io/intro-to-k8s/hello-deployment.yaml`

---

```
$ kubectl apply -f hello-deployment.yaml
deployment "hello" created
```

--

```
$ kubectl get deployments
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hello     3         3         3            0           5s
```

--

```
$ kubectl get replicasets
NAME              DESIRED   CURRENT   READY     AGE
hello-3374368401  3         3         0         30s
```

--

```
$ kubectl get pods
NAME                     READY     STATUS    RESTARTS   AGE
hello-3374368401-735m1   2/2       Running   0          1m
hello-3374368401-exvlo   2/2       Running   0          1m
hello-3374368401-tnhym   2/2       Running   0          1m
```

---

layout: true

## Services

---

```
apiVersion: v1
kind: Service
metadata:
spec:
  ports:
  - port: 8000
  selector:
    app: hello
---
apiVersion: v1
kind: Service
metadata:
spec:
  ports:
  - port: 8080
  selector:
    app: hello
```

`wget https://mstrzele.github.io/intro-to-k8s/hello-svc.yaml`

---

```
$ kubectl apply -f https://mstrzele.github.io/intro-to-k8s/hello-svc.yaml
service "hello-php" created
service "hello-node" created
```

--

```
$ kubectl get svc
NAME         CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
hello-node   10.0.0.150   <nodes>       8080/TCP   7s
hello-php    10.0.0.183   <nodes>       8000/TCP   7s
kubernetes   10.0.0.1     <none>        443/TCP    1h
```

--

```
$ curl $(minikube service hello-php --url)
Hello World
```

--

```
$ open $(minikube service hello-node --url)
```

---

layout: true

## Deployments

---

```
$ kubectl set image deployment hello hello-node=mstrzele/hello-node:v2
deployment "hello" image updated
```

--

```
$ kubectl get pods
NAME                     READY     STATUS        RESTARTS   AGE
hello-3374368401-735m1   2/2       Terminating   0          24m
hello-3374368401-tnhym   2/2       Terminating   0          24m
hello-3455764114-dmrc3   2/2       Running       0          32s
hello-3455764114-jtdv0   2/2       Running       0          32s
hello-3455764114-ovksu   2/2       Running       0          26s
```

---

```
$ kubectl get events | tail -6
3m        3m        1         hello             Deployment                            Normal    ScalingReplicaSet   {deployment-controller }   Scaled up replica set hello-3455764114 to 1
3m        3m        1         hello             Deployment                            Normal    ScalingReplicaSet   {deployment-controller }   Scaled down replica set hello-3374368401 to 2
3m        3m        1         hello             Deployment                            Normal    ScalingReplicaSet   {deployment-controller }   Scaled up replica set hello-3455764114 to 2
2m        2m        1         hello             Deployment                            Normal    ScalingReplicaSet   {deployment-controller }   Scaled down replica set hello-3374368401 to 1
2m        2m        1         hello             Deployment                            Normal    ScalingReplicaSet   {deployment-controller }   Scaled up replica set hello-3455764114 to 3
2m        2m        1         hello             Deployment                            Normal    ScalingReplicaSet   {deployment-controller }   Scaled down replica set hello-3374368401 to 0
```

---

```
$ kubectl get rs
NAME               DESIRED   CURRENT   READY     AGE
hello-3374368401   0         0         0         27m
hello-3455764114   3         3         3         3m
```

--

```
open $(minikube service hello-node --url)
```

---

layout: true

## Pods

---

```
$ for i in {1..3}; do curl $(minikube service hello-php --url); done
Hello World!Hello World!Hello World!%
```

--

```
$ kubectl get pods
NAME                     READY     STATUS    RESTARTS   AGE
hello-3455764114-dmrc3   2/2       Running   0          7m
hello-3455764114-jtdv0   2/2       Running   0          7m
hello-3455764114-ovksu   2/2       Running   0          7m
```

--

```
$ kubectl exec -it hello-3455764114-dmrc3 -c hello-php -- sh
/var/www/html # 
```

--

```
/var/www/html # ps axuww
PID   USER     TIME   COMMAND
    1 root       0:00 php -S 0.0.0.0:8000
   10 root       0:00 sh
   14 root       0:00 ps axuww
```

---

```
/var/www/html # cat index.php
<?php
echo 'Hello World!'
?>
```

--

```
/var/www/html # sed -ie 's/Hello World/Hello Ass, Chessnuts, Guenons/' index.php
```

--

```
/var/www/html # exit
```

--

```
$ for i in {1..3}; do curl $(minikube service hello-php --url); done
Hello Ass, Chessnuts, Guenons!Hello World!Hello World!
```

--

```
$ kubectl delete pod hello-3455764114-dmrc3
pod "hello-3455764114-dmrc3" deleted
```

--

```
$ for i in {1..3}; do curl $(minikube service hello-php --url); done
Hello World!Hello World!Hello World!
```

---

layout: true

## Kubernetes Dashboard

---

```
$ minikube dashboard
```

---

layout: true

## Blue-Green Deployment

---

```
$ wget https://mstrzele.github.io/intro-to-k8s/colors.yaml
--2016-10-10 23:42:18--  https://mstrzele.github.io/intro-to-k8s/colors.yaml
Resolving mstrzele.github.io... 151.101.16.133
Connecting to mstrzele.github.io|151.101.16.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1622 (1.6K) [text/yaml]
Saving to: ‘colors.yaml’

colors.yaml                                                                                100%[========================================================================================================================================================================================================================================>]   1.58K  --.-KB/s    in 0s

2016-10-10 23:42:18 (33.6 MB/s) - ‘colors.yaml’ saved [1622/1622]
```

---

```
$ open colors.yaml
```

--

```
$ kubectl apply -f colors.yaml
service "blue" created
service "green" created
configmap "blue" configured
configmap "green" configured
deployment "blue" created
deployment "green" created
```

--

```
$ kubectl get pods -l app=colors
NAME                    READY     STATUS    RESTARTS   AGE
blue-1308787345-1z59m   1/1       Running   0          2m
blue-1308787345-jci2b   1/1       Running   0          2m
blue-1308787345-z6hmv   1/1       Running   0          2m
green-681673571-r0dvi   1/1       Running   0          2m
```

---

.row[
.col-sm-6[
```
kind: Service
apiVersion: v1
metadata:
  name: colors
  labels:
    app: colors
spec:
  type: NodePort
  ports:
  - port: 8003
    targetPort: 8000
  selector:
    app: colors
    color: blue
```

`wget https://mstrzele.github.io/intro-to-k8s/colors-svc-blue.yaml`
]
.col-sm-6[
```
kind: Service
apiVersion: v1
metadata:
  name: colors
  labels:
    app: colors
spec:
  type: NodePort
  ports:
  - port: 8003
    targetPort: 8000
  selector:
    app: colors
    color: green
```

`wget https://mstrzele.github.io/intro-to-k8s/colors-svc-green.yaml`
]
]

---

```
$ kubectl apply -f colors-svc-blue.yaml
service "colors" created
```

--

```
$ kubectl get svc colors -o wide
NAME      CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE       SELECTOR
colors    10.0.0.164   <nodes>       8003/TCP   46s       app=colors,color=blue
```

--

```
$ open $(minikube service colors --url)
```

--

```
$ kubectl apply -f colors-svc-green.yaml
service "colors" configured
```

--

```
$ kubectl get svc colors -o wide
NAME      CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE       SELECTOR
colors    10.0.0.164   <nodes>       8003/TCP   2m        app=colors,color=green
```

---

layout: true

## Canary Release

---

```
kind: Service
apiVersion: v1
metadata:
  name: colors
  labels:
    app: colors
spec:
  type: NodePort
  ports:
  - port: 8003
    targetPort: 8000
  selector:
    app: colors
```

`wget https://mstrzele.github.io/intro-to-k8s/colors-svc.yaml`

---

```
$ kubectl apply -f colors-svc.yaml
service "colors" configured
```

--

```
$ kubectl get svc colors -o wide
NAME      CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE       SELECTOR
colors    10.0.0.164   <nodes>       8003/TCP   6m        app=colors
```

---

layout: true

---

## Minikube

```
$ minikube stop
Stopping local Kubernetes cluster...
Machine stopped.
```

--

```
$ minikube delete
Deleting local Kubernetes cluster...
Machine deleted.
```

---

background-image: url(http://orullian.com/testpress/wp-content/uploads/2012/10/wallpaper-650912.jpg)

---

layout: true

## Google Container Engine

---

```
$ brew cask install google-cloud-sdk
```

--

```
$ gcloud init
iWelcome! This command will take you through the configuration of gcloud.

Your current configuration has been set to: [default]

Network diagnostic detects and fixes local network connection issues.
Checking network connection...done.
Reachability Check passed.
Network diagnostic (1/1 checks) passed.

You must log in to continue. Would you like to log in (Y/n)? Y
```

--

```
Do you want to configure Google Compute Engine
(https://cloud.google.com/compute) settings (Y/n)?  Y
```

---

```
Which Google Compute Engine zone would you like to use as project
default?
If you do not specify a zone via a command line flag while working
with Compute Engine resources, the default is assumed.
 [1] asia-east1-b
 [2] asia-east1-a
 [3] asia-east1-c
 [4] europe-west1-d
 [5] europe-west1-b
 [6] europe-west1-c
 [7] us-central1-f
 [8] us-central1-b
 [9] us-central1-a
 [10] us-central1-c
 [11] us-east1-c
 [12] us-east1-b
 [13] us-east1-d
 [14] us-west1-a
 [15] us-west1-b
 [16] Do not set default zone
Please enter numeric choice or text value (must exactly match list
item):  5
```

---

```
$ gcloud container clusters get-credentials internal
WARNING: Accessing a Container Engine cluster requires the kubernetes commandline
client [kubectl]. To install, run
  $ gcloud components install kubectl

Fetching cluster endpoint and auth data.
kubeconfig entry generated for internal.
```

--

```
$ kubectl config current-context
gke_letsdeal-bagheera_europe-west1-b_internal
```

--

```
$ rm -fr ~/.kube
```

