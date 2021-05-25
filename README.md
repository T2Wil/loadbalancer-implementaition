# loadbalancer-implementation

This is simple loadbalancer implementation using metalLB.
We are loadbalancing NGINX software as a 

## Tools

- KIND (Kubernetes in Docker): tool for running Kubernetes clusters using docker container. [set KIND on your local machine]()

## How to run this 


`Step 1` : Install metalLB loadbalancer

```
# source: https://kind.sigs.k8s.io/docs/user/loadbalancer/

# switch to metallb directory
cd metallb

# create metallb-system namespace
kubectl apply -f namespace.yaml

# apply metalLB secret
kubectl apply -f secret.yaml

# apply metalLB manifest
kubectl apply -f metallb.yaml

# check if all pods are up (controller and speaker)
kubectl get pods -n metallb-system --watch 

# When all the pods are up and running,
# create a pool/range of IPs that the metalLB loadbalancer will pick from to assign the services
# If using KIND get KIND network in docker: 
docker network inspect -f '{{.IPAM.Config}}' kind

# Apply the address-pool in the configmap
kubectl apply -f metallb-configmap.yaml

```

`Step 2` : Install a service of type loadbalancer

Here we will deploy NGINX software and expose its service as loadbalancer to be assigned an external IP from the metalLB we installed in step 1.

```
# cd into nginx directory
cd ../nginx

# create apps namespace
kubectl apply -f namespace.yaml

# deploy nginx application in apps namespace
kubectl -n apps apply -f deployment.yaml

# deploy nginx svc as a loadbalancer type
kubectl -n apps apply -f service.yaml 


```
