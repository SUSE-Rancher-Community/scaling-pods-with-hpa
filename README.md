# Scaling Pods with HPA
This repository contains source code configure an application to scale horizontally (using the HPA), as well as how to test the scalability of the application. 

## Prerequisites
* [kubectl CLI tool]()
* A Kubernetes cluster - you can provision a k8s cluster quickly with [K3s](), [K3d]() or [RKE](). 

## Install Metrics Server
The HPA will need to receive metrics in order to make decisions about when scaling should occur. You will need to install the metrics server on your K8s cluster with this command:
```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

## Deploy Application
You can deploy the resources in the `hpa-example.yaml` file by running the following command:
```
kubectl create -f hpa-example.yaml
```

## Test Application Scalability
The next step will be to test that your application will scale as expected when the target metric is hit based on the incoming load.

First, have a look at the current usage of your application with the following command:
```
kubectl get hpa -n mc-ns
```

After that, create a shell into a loader container to stress test the application:
```
kubectl run -i --tty loader --image=busybox /bin/sh

while true; do wget -q -O- http://mc-lb.mc-ns.svc.cluster.local; done
```

You can then review the metric usage with the same command you run earlier, as well as keep an eye on the changes the HPA is making to the replica count for you Deployment to handle the traffic:
```
kubectl get hpa -n mc-ns
kubect get pods -n mc-ns
kubectl get deploy mc-web -n mc-ns -o yaml
```
