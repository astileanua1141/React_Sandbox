# Service Bus w/ Kubernetes - Minimal Viable Product
## Description

:checkered_flag: **Objective**: This repository contains a small Microservices application, similar to a Blog, having posts and comments and ... nothing else. The focus of this app was to get out of the comfort zone, and start developing applications in the cloud, not just in my Terminal window.
- learned how to create the tiniest Service Bus
- refreshed my Docker knowledge, learned how to put **Docker** images to good use, with **Kubernetes**: played around with Pods, Services, Deployments, a NodePort, and a Load Balancer provided via **Ingress-nginx**
- created my first "intentional" Cluster

:school: **Course**: [Udemy - Microservices with Node.JS and React](https://www.udemy.com/course/microservices-with-node-js-and-react/)


:cloud: **Technologies**:  
- Node.js
- React.js
- Express.js

:wrench: Tools used: 
- Docker
- Kubernetes
- Minikube 
- Skaffold


:satellite: Services: 
- client
  - the React UI, contains a simple webpage with Posts and Comments, allowing the user to interact with the backend Services: 
    - displays all the Posts and Comments information from the Query service
    - displays custom messages for Comment moderation statuses: pending, rejected, approved
    - creates a new Post item via Posts service
    - creates a new Comment, associated to a Post, via Comments service 
- Posts
  -  manages Posts created per session, notifies the Event Bus of new posts 
- Comments
  - manages Comments created, notifies the Event Bus of new posts, receives Comment updates (ex. when a comment was moderated)
- Event-Bus 
  - the "messenger" component which reaches out to all the other components, it manages events and keeps a record of all the events in case any application (ex. the Query service) needs to retrieve the event history
- Query 
  - holds an in-memory record of all the posts and comments, in order to service the Client with a single request that gets all the data 
- Moderation
  - contains comment moderation logic (ex: Filters and rejects all comments containing the word "orange")

:mag: **Resources**:  
  - [Minikube installation guide on Ubuntu 22.04](https://www.linuxtechi.com/how-to-install-minikube-on-ubuntu/)
  
  - Install Ingress for minikube: 
    > `$ minikube addons enable ingress `

  - [Skaffold Quick Start](https://skaffold.dev/docs/quickstart/)

  
## Useful commands:
---

From computer start: 
--
`$ minikube start`

Build a new image with docker 

go to the folder where dockerfile is (/posts)

`$ docker build -t alexandra1141/posts . ` 

`$ docker push alexandra1141/posts `

restart the k8s deployment: 

go to /infra/k8s folder (where yaml file is) 

`$ kubectl rollout restart deployment posts-depl `

verify pods - age must be fresh

`$ kubectl get pods `

The setup has a NodePort : 

 find the ip address of minikube

`$ minikube ip` 
> 192.168.49.2


**Note**: add this IP mapping to /etc/hosts on Linux, to override outgoing request to "posts.com"

(ingress-srv.yaml named the Load Balancer as "posts.com" in `spec > rules > host: posts.com` )

> `192.168.49.2      posts.com`


find the port of nodeport 

`$ k get service`
 
 ```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE`
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          16h
posts-srv    NodePort    10.96.218.233   <none>        4000:32208/TCP   15h`

```


## Issues
---
- [X] Axios crashed the nodemon when a component was not available
  - solution 1: wrap in a try/catch (did not fix the issue though)
  - solution 2: install axios as an extra step in Dockerfile (see client)
- note: similar issue when starting services with skaffold 
    - query service failed at initialization due to dependency on event-bus
    - solution: distancing the event-bus initialization and query initialization to allow those extra milliseconds for event-bus to be available

- sample log:
```
node:internal/process/promises:288
            triggerUncaughtException(err, true /* fromPromise */);
            ^
AxiosError: connect ECONNREFUSED 127.0.0.1:4005
    at TCPConnectWrap.afterConnect [as oncomplete] (node:net:1300:16) {
  port: 4005,
  address: '127.0.0.1',
  syscall: 'connect',
  code: 'ECONNREFUSED',
  errno: -111,
  config: {
    transitional: {
      silentJSONParsing: true,
      forcedJSONParsing: true,
      clarifyTimeoutError: false
    },
...
```








