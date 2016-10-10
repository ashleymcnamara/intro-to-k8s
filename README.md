class: center, middle

# Introduction to Kubernetes

---

name: kubernetes
class: middle
background-image: url(https://i.kinja-img.com/gawker-media/image/upload/gf2yaqbwgtfeodvlyiga.jpg)
layout: true

---

.container-fluid[
## Kubernetes

* **K8s** is an abbreviation derived by replacing the 8 letters _ubernete_ with 8.
* Greek for _Helmsman_; also the root of the words _governor_ and _cybernetic_.
* Open-source platform for automating deployment, scaling, and operations of application container
* Similar to Docker Engine (or Docker Swarm) combined with Docker Compose.
]

---

.container-fluid[
## Kubernetes

* Kubernetes is a framework for building distributed systems.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">The Kubernetes project is hitting phase two; that&#39;s where the distros come in. Take your pick from Dies, OpenShift, Tectonic, GKE, and more.</p>&mdash; Kelsey Hightower (@kelseyhightower) <a href="https://twitter.com/kelseyhightower/status/775494220278738944">September 13, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
]

---

name: architecture
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

name: pods
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
  name: nginx
spec:
  containers:
  - name: nginx
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

name: labels

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

name: annotations

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

name:  selectors
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

name: services
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
  name: myapp
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
  * Map the service to the contents of the `externalName field` (e.g. `foo.bar.example.com`), by returning a `CNAME` record with its value.
  * No proxying of any kind is set up. 
]

---

name: volumes
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
  name: redis
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-persistent-storage
      mountPath: /data/redis
  volumes:
  - name: redis-persistent-storage
    emptyDir: {}
```
]

---

name: jobs
class: middle
background-image: url(http://antyweb.pl/wp-content/uploads/2015/11/SteveJobsCloseUp.jpeg)
layout: true

---

.container-fluid[
## Jobs
]

---

name: others
class: middle
background-image: url(http://i.bullfax.com/imgs/1fcc63a04b1534ad2f9664ec286698041b103155.jpg)
layout: true

---

.container-fluid[
## Others

* `PersistentVolume`
* `PersistentVolumeClaim`
* `HorizontalPodAutoscaler`
* `v1beta1.DaemonSet`
* `v1beta1.ScheduledJob`
* ...
]
