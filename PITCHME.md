---?image=assets/images/ship-wheel.jpg&opacity=80&size=crop

@title[Let's Get Started]

### Introduction to

# Kubernetes

###### alexyeung@hk01

---?image=https://d33wubrfki0l68.cloudfront.net/e7b766e0175f30ae37f7e0e349b87cfe2034a1ae/3e391/images/docs/why_containers.svg&color=#fff&size=auto 90%

@title[Why Container?]

Note:

### Old way

1. depends on OS package manager, all dependencies and their config are coupled

    - ex: like you won't share your node_modules for all projects right?
    - what if different node version is needed?

### New way

1. each containers are isolated from each other and the host, and having their own config, such as node version, timezone

1. portable

    - easier to scale horizontally
    - combinations can be infinite
