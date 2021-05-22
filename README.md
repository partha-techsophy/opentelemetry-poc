# Tracing in Spring Boot with OpenTelemetry

Distributed systems require something to trace what is happening inside a software architecture, useful for a lot of things (first of all troubleshooting).


## Project details
We will create 3 projects as listed bellow
- API Gateway - Route all incoming traffics and secure all backend services.
- Service-1 - A `hello` service
- Service-2 - A `hello` service


## Pre requisite
- Docker
- Docker-Compose
- Minikube - To run local kuberenetes
- kubectl - Kubernetes CLI
- Skaffold - To deploy and test in kbernetes
- OpenTelemetry - An observability framework for cloud-native software.
- Jaeger - Open source, end-to-end distributed tracing.
- Java SDK 11.x
- IDE (Intellij/ Eclipse)


## Installation
- [Install docker](https://docs.docker.com/engine/install/)
- [Install docker-compose](https://docs.docker.com/compose/install)
- [Install minikube](https://minikube.sigs.k8s.io/docs/start/)
- [Install kubectl](https://kubernetes.io/docs/tasks/tools/)
- [install skaffold](https://skaffold.dev/docs/install/)

## Run minikube
To start minikube execute the following command
```
$ minikube start
```

Enable addon Ingress
```
$ minikube addons enable ingress
```

Enable load balancer, keep this programm running in a separate window
```
$ minikube tunnel
``` 

## Run Jeager all-in-one package.
It launches the Jaeger UI, collector, query, and agent, with an in-memory storage component.

```
$ cd jaeger
$ docker-compose up
```

Go to Jaeger UI http://localhost:16686/search 

## Gateway project
Configure the gateway project.

- Open build.gradle and replace the IP_ADDRESS with your ip address in `jib` section for the jaeger endpoint.  

Deploye the gateway application

```
$ skaffold run
```
To undeploy the application from kubernetes run the following command
```
$ skaffold delete
```

To check if deployment is complete run the following command in a terminal. 

```
$ kubectl get po -w
```

Output of the above command will show pod deployment in progress as shown below.
```
NAME                            READY   STATUS    RESTARTS   AGE
cloud-gateway-cdcdf45df-m5gs5   0/1     Pending   0          1s
```

To view application log run the following command using the name from above list
```
kubectl logs -f <NAME>
```

List the external IP address `kubectl get svc`


> Note: You may get the following error; 
Message: Forbidden!Configured service account doesn’t have access. Service account may have been revoked. endpoints is forbidden: User "system:serviceaccount:demo:default" cannot list endpoints in the namespace "demo": no RBAC policy matched.`

To resolve the above error run the following command and deploy the application again.

``` 
$ kubectl create clusterrolebinding admin --clusterrole=cluster-admin --serviceaccount=default:default
```

## Service-1
Configure the service project before deploying. 

- Open build.gradle and replace the IP_ADDRESS with your ip address in `jib` section for the jaeger endpoint.  


Deploy the applicaion same as the Gateway application

To test the `hello` service in browswer type `<GATEWAY_IP:PORT>/service-1/hello`. This will return a greeting message.

## Service-2
Configure the service project before deploying. 

- Open build.gradle and replace the IP_ADDRESS with your ip address in `jib` section for the jaeger endpoint.  

Deploy the applicaion same as the Gateway application

To test the `hello` service in browswer type `<GATEWAY_IP:PORT>/service-2/hello`. This will return a greeting message.

## Jaeger UI
Go to Jaeger UI http://localhost:16686/search 

There are a combo list with services and using the search we can see the traces related to a specific service, with a quick overview that tell us also the response time of traces. If want to see the details of a trace we can click on it.


## Cleanup 
To cleanup runn the following command in sequence
```
$ service-2/skaffold delete

$ service-1/skaffold delete

$ cloud-gateway/skaffold delete

$ Ctrl+c to terminate minikube tunnel

$ mininube stop

$ Ctrl+c to terminate jaeger application
```