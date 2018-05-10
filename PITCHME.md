---?image=assets/images/k8s-frontpage.jpg&size=crop

@title[Let's Get Started]

### Introduction to

# @css[text-stroke](Kubernetes)

###### alexyeung@hk01

Note:

- sharing of basic learning from aws kubernetes workshop
- introduction level

---?color=dodgerblue

@title[Why Container?]

# @color[#fff](Why Container?)

---?image=https://d33wubrfki0l68.cloudfront.net/e7b766e0175f30ae37f7e0e349b87cfe2034a1ae/3e391/images/docs/why_containers.svg&size=auto 90%

@title[Compare VM With Container]

Note:

In context of decoupling deployed app

### Old way

1. depends on OS package manager, all dependencies and their config are coupled

    - ex: like you won't share your node_modules for all projects right?
    - what if different node version is needed?

1. waste resource when auto scale => not all apps need to scale but they are forced to scale tgt

1. some will just ship one app in one machine

    - low resource utilization
    - e.g. one time promotion website
    - more machines to maintain

### New way

1. each containers are isolated from each other and the host, and having their own config, such as node version, timezone

    - do not need to worry dependency interference

1. portable

    - easier to scale horizontally
    - combinations can be infinite

---?color=dodgerblue

@title[Introducing Kubernetes]

## @color[#fff](Introducing Kubernetes)

#### @color[#fff](an open source container management system developed by Google)

---?image=assets/images/k8s-overview-architecture.png&size=contain&color=#e4ebf2

@title[Kubernetes Overview Architecture]

Note:

- consist of Master Nodes and Worker Nodes
    - master/slave architecture

---?image=assets/images/k8s-master-architecture.png&size=contain&color=#e4ebf2

@title[Kubernetes Master Architecture]

+++?image=assets/images/k8s-master-desc.jpg&size=contain

@title[Kubernetes Master Description]

Note:

- api server
    - interface to start/stop pods, can call via api, ui and cli
- etcd
    - which pod is deployed and on which nodes
- scheduler
    - decide which node should the pod be deployed to
- controller manager
    - make sure pod are running in desired number of replication
    - detect down node and take action (failover)
- k8s 1.6+: cloud-controller-manager
    - integrate with cloud provider like aws, gcp
    - e.g. config load balanacer for Service

---?image=assets/images/k8s-node-architecture.png&size=contain&color=#e4ebf2

@title[Kubernetes Node Architecture]

Note:

- pod
    - explain what is pod

+++?image=assets/images/k8s-node-desc.jpg&size=contain

@title[Kubernetes Node Description]

Note:

- kubelet
- kube-proxy
- fluentd

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
          memory: "200Mi"
          cpu: "700m"
        requests:
          memory: "200Mi"
          cpu: "700m"
```

@[10, 14](Requests: minimum resource required)
@[10, 11](Limits: maximum resource can be used)
@[16](number of CPU core, such as "1" or "0.1")
@[15](amount of Memory, such as "512Mi")
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
- k8s helps pack as many pods onto as a few nodes as possible

---?image=assets/images/k8s-flow.jpg&size=contain

@title[Kubernetes CI/CD Flow]

---?color=dodgerblue

@title[Reasons to use Kubernetes]

# @color[#fff](Why Kubernetes?)

- @color[#fff](containers management)

- @color[#fff](microservices friendly)

- @color[#fff](portable in any cloud environments)

---

@title[References]

## References

- <https://kubernetes.io/docs/concepts>

- <https://jimmysong.io/kubernetes-handbook>

- <https://thenewstack.io/kubernetes-an-overview>

- <https://docs.openshift.com/container-platform/3.7/dev_guide/compute_resources.html>

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
