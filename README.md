# Kubernetes

> Perso kubernetes doc

Main Contacts:

- Thomas Plantain: thomas.plantain@gmail.com


## Table of Content
- [Kubernetes](#kubernetes)
  - [Table of Content](#table-of-content)
  - [Changelog](#changelog)
  - [Related Documents](#related-documents)
  - [Systems](#systems)
  - [Big Picture](#big-picture)
  - [Features](#features)
    - [Install](#install)
    - [Kubernetes commands](#kubernetes-commands)
    - [INGRESS](#ingress)
      - [ingress-bot.yaml](#ingress-botyaml)
      - [ingress-bot-path](#ingress-bot-path)


## Changelog

| Date | Author | Modifications |
| --- | --- | --- |
| 07/2022 | Thomas | Init repo |

## Related Documents

| Name | Type | Description |
| --- | --- | --- |
| Microk8s | site web | [microk8s.io](https://microk8s.io/) |


## Systems

Here are the systems used

| System Name | Description |
| --- |  --- |
| Oracle Virtualbox | {description} |
| Debian | {description} |
| micork8s | {description} |

## Big Picture

![Kubernetes components](./images/components-of-kubernetes.svg)


## Features

Features are:

- **Install microk8s**: {description}
- **Ingress presentation**: {description}.

In the following parts we will detail those features with the dependencies to each systems and the current considerations.

### Install

- First install snap
- Run snap install:

``sudo snap install microk8s --classic``

### Kubernetes commands

- link microk8s to /root/snap/bin/microk8s
- kubernetes dashboard:
``microk8s kubectl get all –all-namespaces``


- kubernetes status: 
``microk8s status``


- Create a microbot deployment with two pods via the kubectl cli
```
  microk8s kubectl create deployment microbot --image=dontrebootme/microbot:v1
  microk8s kubectl scale deployment microbot –replicas=2


  microk8s kubectl create deployment nginx –image=nginx
  microk8s kubectl scale deployment nginx –replicas=2
```

- Dashboard
``microk8s dashboard-proxy``


- To expose our deployment we need to create a service
```
  microk8s kubectl expose deployment microbot --type=NodePort --port=80 --name=microbot-service
  microk8s kubectl expose deployment nginx --type=NodePort --port=80 –name=nginx-service
```
- commands:
```
  microk8s status: Provides an overview of the MicroK8s state (running / not running) as well as the set of enabled addons
  microk8s enable: Enables an addon
  microk8s disable: Disables an addon
  microk8s kubectl: Interact with kubernetes
  microk8s config: Shows the kubernetes config file
  microk8s istioctl: Interact with the istio services; needs the istio addon to be enabled
  microk8s inspect: Performs a quick inspection of the MicroK8s intallation
  microk8s reset: Resets the infrastructure to a clean state
  microk8s stop: Stops all kubernetes services
  microk8s start: Starts MicroK8s after it is being stopped
```


### INGRESS


#### ingress-bot.yaml

```
  root@kube:~# cat /home/thomas/Documents/ingress-bot.yaml 
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: test-ingress
  spec:
    defaultBackend:
      service:
        name: microbot-service
        port:
          number: 80
```


- create ingress rule: ``micro8ks kubectl create -f ingress-bot.yaml``
- Get ingress rules : ``micro8ks kubectl get ingress``

```
  root@kube:~# microk8s kubectl get ingress
  NAME           CLASS    HOSTS   ADDRESS   PORTS   AGE
  test-ingress   public   *                 80      39s
```

- http://127.0.0.1/


- ``micro8ks kubectl create -f ingress-bot-path.yaml``

- http://127.0.0.1/bot

```
  root@kube:/home/thomas/Documents# microk8s kubectl get ingress
  NAME           CLASS    HOSTS   ADDRESS     PORTS   AGE
  test-ingress   public   *       127.0.0.1   80      8d
  path-ingress   public   *                   80      14s
```

#### ingress-bot-path
```
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: path-ingress
    annotations:
      nginx.ingress.kubernetes.io/rewrite-target: /
  spec:
    rules:
    - http:
        paths:
        - path: /bot
          pathType: Prefix
          backend:
            service:
              name: microbot-service
              port:
                number: 80
```



- Use command: ``microk8s ctr images list``
