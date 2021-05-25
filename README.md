# loadbalancer-implementation in kubernetes cluster

This is simple loadbalancer implementation using metalLB.
We are loadbalancing NGINX software in this example

## Tools

- KIND (Kubernetes in Docker): tool for running Kubernetes clusters using docker container. [set KIND on your local machine](https://www.notion.so/Create-Kubernetes-cluster-with-KinD-2f20ca99835a4df6bc223ec62c05b5e7)

## How to run this 


`STEP 1` : Install metalLB loadbalancer

source: https://kind.sigs.k8s.io/docs/user/loadbalancer/

- switch to metallb directory
```
cd metallb
```

- create metallb-system namespace
```
kubectl apply -f namespace.yaml
```

- apply metalLB secret
```
kubectl apply -f secret.yaml
```


- apply metalLB manifest
```
kubectl apply -f metallb.yaml
```

- check if all pods are up (controller and speaker)
```
kubectl get pods -n metallb-system --watch 
```

![image](https://user-images.githubusercontent.com/30593186/119572038-72a24e80-bdb2-11eb-9108-84266d69abc9.png)



- When all the pods are up and running, create a pool/range of IPs that the metalLB loadbalancer will pick from to assign the services
  If using KIND get KIND network in docker: 
```
docker network inspect -f '{{.IPAM.Config}}' kind
```

Should be something like this:

![image](https://user-images.githubusercontent.com/30593186/119572216-b39a6300-bdb2-11eb-83d7-f4ff9779663d.png)


- Apply the address-pool in the configmap
```
kubectl apply -f metallb-configmap.yaml

```



`STEP 2` : Install a service of type loadbalancer

Here we will deploy NGINX software and expose its service as loadbalancer to be assigned an external IP from the metalLB we installed in step 1.


- cd into nginx directory
```
cd ../nginx
```
- create apps namespace
```
kubectl apply -f namespace.yaml
```

- deploy nginx application in apps namespace
```
kubectl -n apps apply -f deployment.yaml
```

- deploy nginx svc as a loadbalancer type
```
kubectl -n apps apply -f service.yaml 

```

Results:

![image](https://user-images.githubusercontent.com/30593186/119572391-f2301d80-bdb2-11eb-9847-f9c6f44ee59f.png)


![image](https://user-images.githubusercontent.com/30593186/119572463-10961900-bdb3-11eb-9c3a-dbc3df64d84d.png)


