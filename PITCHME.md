---?image=assets/images/k8s-frontpage.jpg&size=crop

@title[Let's Get Started]

### Introduction to

# @css[text-stroke](Kubernetes)

###### alexyeung@hk01

Note:

- sharing of basic learning from aws kubernetes workshop
- focus on why use k8s and what is its advantage

---?color=dodgerblue

@title[Why Container?]

# @color[#fff](Why Container?)

Note:

- k8s is based on container

---?image=https://d33wubrfki0l68.cloudfront.net/e7b766e0175f30ae37f7e0e349b87cfe2034a1ae/3e391/images/docs/why_containers.svg&size=auto 90%

@title[Compare VM With Container]

Note:

In context of decoupling deployed app

### VM Way

1. depends on OS package manager, all dependencies and their config are coupled

    1. what if different node version is needed?
    1. if upgrade node version
        1. all apps need to test if broken
        1. if breaking changes, all apps need to update

1. some will just ship one app in one machine

    - low resource utilization
    - e.g. one time promotion website
    - more machines to maintain

### Container Way

1. each containers are isolated from each other and the host, and having their own config, such as node version, timezone

    - do not need to worry dependency interference

1. portable

    - easier to scale horizontally

1. container is defined by developer
    - can ensure same environment as development

---?color=dodgerblue

@title[Introducing Kubernetes]

## @color[#fff](Introducing Kubernetes)

#### @color[#fff](an open source container management system developed by Google)

---?image=assets/images/k8s-overview-architecture.png&size=contain&color=#e4ebf2

@title[Kubernetes Overview Architecture]

Note:

- consist of Master Nodes and Worker Nodes
    - master nodes control worker nodes
- api
    - allow engineer to control worker nodes via master nodes

+++

@title[Kubernetes Object]

#### Kubernetes Object

```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: demo-deployment
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
        -
          name: demo
          image: gcr.io/project-id/demo:latest
  selector:
    matchLabels:
      app: demo
```

@[1, 6, 12-15]

Note:

- you can feed a config to api, and master will ensure worker nodes follow your instruction
- this spec is asking master to ensure demo container with 3 replicas
- it is infrastructure independenced
    1. write once run everywhere
    1. k8s almost abstract all infrastructure for you
    1. why?
        - easy to migrate, no vender lock
            - you never sure if this cloud provider provides all services you need
            - e.g. you may want to deploy to alicloud if you start business in china
            - e.g. overcharge, google app engine happened once, see
                - <https://startupsventurecapital.com/firebase-costs-increased-by-7-000-81dc0a27271d>
                - <https://hackernoon.com/why-and-how-we-left-app-engine-after-it-almost-destroyed-us-40ac2fc0b1a8>

---?image=assets/images/k8s-master-architecture.png&size=contain&color=#e4ebf2

@title[Kubernetes Master Architecture]

+++?image=assets/images/k8s-master-desc.jpg&size=contain

@title[Kubernetes Master Description]

Note:

- pod
    - you can think of container first
- etcd
    - which pod is deployed and on which nodes
- api server
    - interface to start/stop pods, can call via api, ui and cli
- controller manager
    - make sure pod are running in desired number of replication
    - detect down node and take action (self healing)
- scheduler
    - decide which node should the pod be deployed to
- k8s 1.6+: cloud-controller-manager
    - integrate with cloud provider like aws, gcp
    - e.g. config load balanacer for Service

---?image=assets/images/k8s-node-architecture.png&size=contain&color=#e4ebf2

@title[Kubernetes Node Architecture]

Note:

- pods are in different size

+++

@title[Resource Quotas]

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: qos-demo
spec:
  containers:
    -
      name: qos-demo-ctr
      image: nginx
      resources:
        limits:
          memory: "200Mi" # e.g. "300Mi" for Burstable
          cpu: "700m"
        requests:
          memory: "200Mi"
          cpu: "700m"
```

@[10, 14](Requests: minimum resource required)
@[16](number of CPU core, such as "1" or "0.1")
@[15](amount of Memory, such as "512Mi")
@[10, 11](Limits: maximum resource can be used)
@[1-9](if not all limit and request are defined, QoS class = "BestEffort")
@[11-12, 14-15](if any limit > request, QoS class = "Burstable")
@[11-16](if all limits and requests are the same, QoS class = "Guaranteed")

<br />

Note:

- if only limit is set, but not request
    - request is set to be the same as limit
- if only request is set, but not limit
    - use default value in this namespace
    - if no default value, will use all available resource

+++?image=assets/images/grid-cats.jpg&size=crop

@title[Analogy of multiple pods with different resource limit]

# @css[text-stroke](Bin Packing Problem)

Note:

- pack as much as small boxes in a fixed number and capacity of bin
    - each bin has different width, height and depth
    - in k8s, it is cpu and memory limit
- k8s helps pack as many pods onto as a few nodes as possible

+++?image=assets/images/k8s-cluster-overview.jpg&size=contain

@title[Kubernetes Cluster Overview]

Note:

- for example, if set Pod 2 to have 3 replica sets, it will be deployed to Node 3
- can take more metrics
    - Pod anti-affinity (beta) to try avoid deploying same pod in same node, to ensure HA, and deployed to Node 2

---?image=assets/images/k8s-node-architecture.png&size=contain&color=#e4ebf2

@title[Kubernetes Node Architecture Part.2]

Note:

- fluentd

+++?image=assets/images/container-logging.jpg&size=contain&color=#e6f7fe

@title[Centralized Logging]

Note:

- k8s default come up with fluentd to stream all logs to centralized log storage
- there are multiple pods, it is non sense to check log in each pods manually
- pod will be killed (not enough resource / redeploy), log will be deleted tgt, same as current vm approach
- it is used by default in some host kubernetes environment such as GCP kubernetes engine

+++?image=assets/images/service-discovery.jpg&size=crop

@title[Service Discovery]

# @css[text-stroke](Service Discovery)

+++?image=assets/images/why-service-discovery.png&size=contain

@title[Why Service Discovery]

Note:

- it is very common in microservice architecture that one service needs to communicate with another
- it is impossible to use IP address to communicate as IP is dynamic and only point to one instance

+++?image=assets/images/server-side-service-discovery.jpg&size=contain

@title[Server Side Service Discovery]

Note:

- k8s did it for you
- create a Service Object, the Service will monitor all related pod's IP
- all related pod's IP is saved in etcd
- load balancer will read service registry and forward consumer to any one pod

---?color=dodgerblue

@title[Reasons to use Kubernetes]

# @color[#fff](Why Kubernetes?)

- @color[#fff](containers management)

- @color[#fff](microservices friendly)

- @color[#fff](portable in any cloud environments)

Note:

- containers management
    - auto assign pod to diff nodes
    - auto failover (replica)
    - self healing
    - centralized logging
    - resource quota so no single pod can take all resources
    - *bonus*: it can do auto scaling on pod and nod based on resource usage
- microservices friendly
    - service discovery
- portable in any cloud environments
    - open source, can run in any infrastructure
    - abstracted infrastructure level operation
    - write once run everywhere

---

@title[References]

## References

- <https://kubernetes.io/docs/concepts>
- <https://jimmysong.io/kubernetes-handbook>
- <https://thenewstack.io/kubernetes-an-overview>
- <https://docs.openshift.com/container-platform/3.7/dev_guide/compute_resources.html>
- <http://www.informaticslab.co.uk/technical/infrastructure/2016/03/30/kubernetes-in-pics.html>

+++

@title[References Part 2]

## References Part 2

- <https://blog.treasuredata.com/blog/2016/06/14/fluentd-kubernetes-and-google-cloud-platform-a-few-recipes-for-streaming-logging>
- <https://stackoverflow.com/questions/45945011/on-gke-how-to-evenly-distribute-pod-replica-on-nodes>
- <https://www.nginx.com/blog/service-discovery-in-a-microservices-architecture>

---

@title[Tutorials]

## Tutorials

- <https://github.com/aws-samples/aws-workshop-for-kubernetes>

- <https://kubernetes.io/docs/tutorials>

---?color=yellow

@title[Bonus]

# @color[dodgerblue](*Bonus*)

+++

@title[waifu2x]

# waifu2x

### Image Super-Resolution using Deep Convolutional Neural Networks

+++?image=assets/images/cat-upscale-compare.jpg&size=contain

### @css[position-bottom-left text-stroke](Original)

### @css[position-bottom-right text-stroke](Upscaled)

+++

@title[waifu2x Source & Live Demo]

### Source Code

- <https://github.com/nagadomi/waifu2x>

<br />

### Live Demo

- <https://bigjpg.com/>

---?color=dodgerblue

@title[The End]

# @color[#fff](Questions?)

---

@title[Appendix]

# Appendix

+++?image=assets/images/k8s-flow.jpg&size=contain

@title[Kubernetes CI/CD Flow]

+++

### Quality of Service (CPU)

- @size[0.6em](A BestEffort CPU container is able to consume as much CPU as is available on a node but runs with the lowest priority.)
- @size[0.6em](A Burstable CPU container is guaranteed to get the minimum amount of CPU requested, but it may or may not get additional CPU time. Excess CPU resources are distributed based on the amount requested across all containers on the node.)
- @size[0.6em](A Guaranteed CPU container is guaranteed to get the amount requested and no more, even if there are additional CPU cycles available. This provides a consistent level of performance independent of other activity on the node.)

+++

### Quality of Service (Memory)

- @size[0.6em](A BestEffort memory container is able to consume as much memory as is available on the node, but there are no guarantees that the scheduler will place that container on a node with enough memory to meet its needs. In addition, a BestEffort container has the greatest chance of being killed if there is an out of memory event on the node.)
- @size[0.6em](A Burstable memory container is scheduled on the node to get the amount of memory requested, but it may consume more. If there is an out of memory event on the node, Burstable containers are killed after BestEffort containers when attempting to recover memory.)
- @size[0.6em](A Guaranteed memory container gets the amount of memory requested, but no more. In the event of an out of memory event, it will only be killed if there are no more BestEffort or Burstable containers on the system.)

+++

### Horizontal Pod Autoscaler

```yaml
kind: HorizontalPodAutoscaler
apiVersion: autoscaling/v2beta1
metadata:
  name: php-apache
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache
  minReplicas: 1
  maxReplicas: 10
  metrics:
    -
      type: Resource
      resource:
        name: cpu
        targetAverageUtilization: 50
```

@[10-11, 16-17]
