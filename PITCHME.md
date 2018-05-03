---?image=assets/images/k8s-frontpage.jpg&size=crop

@title[Let's Get Started]

### Introduction to

# @css[text-stroke](Kubernetes)

###### alexyeung@hk01

---?image=https://d33wubrfki0l68.cloudfront.net/e7b766e0175f30ae37f7e0e349b87cfe2034a1ae/3e391/images/docs/why_containers.svg&size=auto 90%

@title[Why Container?]

Note:

In context of decoupling deployed app

### Old way

1. depends on OS package manager, all dependencies and their config are coupled

    - ex: like you won't share your node_modules for all projects right?
    - what if different node version is needed?

### New way

1. each containers are isolated from each other and the host, and having their own config, such as node version, timezone

1. portable

    - easier to scale horizontally
    - combinations can be infinite

---?image=assets/images/microservices-concerns.jpg&size=contain

@title[Microservices Concerns]

---?image=assets/images/k8s-overview-architecture.png&size=contain

@title[Kubernetes Overview Architecture]

---?image=assets/images/k8s-master-architecture.png&size=contain

@title[Kubernetes Master Architecture]

---?image=assets/images/k8s-node-architecture.png&size=contain

@title[Kubernetes Node Architecture]

---?image=assets/images/grid-cats.jpg&size=crop

@title[Analogy of multiple pods with different resource limit]

# @css[text-stroke](Bin Packing problem)

Note:

pack as many pods onto as a few nodes as possible

---?image=assets/images/k8s-flow.jpg&size=contain

@title[Kubernetes CI/CD Flow]

---

@title[The End]

# Questions?
