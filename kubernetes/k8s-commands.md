# General Resources
* [kubectl cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

# kubectl context 
```
kubectl cluster-info
kubectl config get-contexts
```

## Use a context
```
kubectl config use-context docker-desktop
```

## View current context
``` 
kubectl config current-context
```

## View your environment's `kubeconfig`
``` 
kubectl config view
```

## Unset context
``` 
kubectl config unset context.<your-context-name>
```
[Generating a kubconfig entry for glcoud](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl)

## cluster, credentials and context config

```sh 
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://${KUBERNETES_PUBLIC_ADDRESS}:6443 \
    --kubeconfig=${instance}.kubeconfig

  kubectl config set-credentials system:node:${instance} \
    --client-certificate=${instance}.pem \
    --client-key=${instance}-key.pem \
    --embed-certs=true \
    --kubeconfig=${instance}.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:${instance} \
    --kubeconfig=${instance}.kubeconfig


```

# kubectl rollout
```
kubectl rollout status deploy worker
kubectl rollout history deployment worker
```

```
kubectl rollout undo deploy worker
kubectl rollout undo deployment worker --to-revision=1
```


# kubectl describe
```
kubectl describe -n kube-system
kubectl describe -n kube-system ds/traefik-ingress-controller
```
# kubectl diff
```
kubectl diff -f just-a-pod.yaml
```

# kubectl api-resources
```
kubectl api-resources
```

# kubectl api-versions
```
kubectl api-versions
```

# kubectl get api-services

```
kubectl get apiservices
```

# kubectl explain
```
kubectl explain Pod.spec
kubectl explain Pod.spec.containers
kubectl explain Pod.spec.containers.workingDir
kubectl explain Pod.spec.containers.tty
kubectl explain Pod.spec --recursive
```

# kubectl apply
```
kubectl apply -f https://k8smastery.com/dockercoins.yaml
kubectl apply -f rng.yaml
kubectl apply -f rng.yaml --validate=false
kubectl apply -f web.yaml --dry-run --validate=false
kubectl apply -f web.yaml --dry-run --validate=false -o yaml
kubectl apply -f web.yaml --dry-run=server --validate=false -o yaml
```

## 

Apply every file in a directory (way of decomposing multiple yaml files)
```
kubectl apply -f .
```

# kubectl delete

```
kubectl delete -f https://k8smastery.com/dockercoins.yaml
kubectl delete deployment/httpenv service/httpenv
```

# Deployments

What is a deployment?
* Wraps a pod
* provides scaling, rolling updates


```
kubectl create deployment httpenv --image=bretfisher/httpenv
kubectl create deployment redis --image=redis
kubectl create deployment hasher --image=dockercoins/hasher:v0.1

kubectl create deployment web --image nginx -o yaml --dry-run
kubectl create deployment web --image=nginx -o yaml > web.yaml

kubectl get deployments -w
kubectl set image deploy worker worker=dockercoins/worker:v0.2
kubectl set image deploy worker worker=dockercoins/worker:v0.3
kubectl edit deployment worker
```

## Get deployments
```
kubectl get deployments -w
```

## Scale deployments
```
kubectl scale deployment httpenv --replicas 10
kubectl scale deployment worker --replicas=2
kubectl scale deployment worker --replicas=3
kubectl scale deployment worker --replicas=10
```

## Expose deployments
* Expose a resource as a new kubernetes service
```
kubectl expose deployment redis --port 6379
kubectl expose deployment httpenv --port 8888
kubectl expose deployment rng --port 80
kubectl expose deployment hasher --port 80
kubectl expose deployment cheddar --port=80
kubectl expose deployment stilton --port=80
kubectl expose deployment wensleydale --port=80
```

* Expose a specific port on a Node (VM)
```
kubectl expose deploy/webui --type=NodePort --port=80
```

# Nodes
```
kubectl get nodes
```

# Pods

What are pods? 
* Contains one or more containers
* atomic unit of scheduling

Label pods
```
kubectl label pods -l app=rng enabled=yes
```

Get pods and watch flag
```
kubectl get pod -w
```


# Endpoints
```
kubectl get endpoints
kubectl get endpoints httpenv -o yaml
```


# Services

What is a Service?
* Stable interface in front a Pods.  Name and IP does not change.
* Name and IP get registered with the cluster's DNS service (CoreDNS)
```
kubectl edit service rng
kubectl get svc
kubectl get svc dashboard
```


Example

```sh
$> kubectl get service
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
dashboard    NodePort    10.102.159.47   <none>        80:30233/TCP   6d
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        10d
```

Example

```sh
$> kubectl describe service dashboard
Name:                     dashboard
Namespace:                default
Labels:                   app=dashboard
Annotations:              <none>
Selector:                 app=dashboard
Type:                     NodePort
IP:                       10.102.159.47
LoadBalancer Ingress:     localhost
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30233/TCP
Endpoints:                10.1.0.57:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```


# Logs

```sh
kubectl logs deploy/rng
kubectl logs deploy/worker
kubectl logs deploy/worker --follow

kubectl logs --tail 1 --follow $POD
kubectl logs -l deployment=nginx 
kubectl logs -l deployment=nginx --tail 100
kubectl logs deploy/nginx 
kubectl logs deploy/nginx --tail 100 --follow
```

# Other/Unknown/Parking Lot

```
kubectl get ingress
```

## kubectl get all 

```sh
$> kubectl get all
NAME                             READY   STATUS    RESTARTS   AGE
pod/dashboard-79c6957b6f-bjdqp   1/1     Running   2          6d

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/dashboard    NodePort    10.102.159.47   <none>        80:30233/TCP   6d
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        10d

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/dashboard   1/1     1            1           6d

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/dashboard-79c6957b6f   1         1         1       6d

```

# Replicasets

```
kubectl get replicasets -w
kubectl get rs
```

# Daemonsets

What is a daemonset? 
* Ensures that one and only one pod is deployed on a node.

```
kubectl delete daemonset/rng
```

# Namespaces
```
kubectl create namespace awesome-app -o yaml --dry-run
```

# Examples of go-template output

```
HASHER=$(kubectl get svc hasher -o go-template={{.spec.clusterIP}})
RNG=$(kubectl get svc rng -o go-template={{.spec.clusterIP}})
```

# Examples of json output and jq
```
kubectl get deploy -o json | jq ".items[] | {name:.metadata.name} + .spec.strategy.rollingUpdate"
```

# Examples of jsonpath output
```sh
NODE_PORT=$(kubectl get svc nginx  --output=jsonpath='{range .spec.ports[0]}{.nodePort}')
```

# Secrets

```sh
kubectl get secrets
kubectl get secrets -o yaml

kubectl create secret generic kubernetes-the-hard-way  --from-literal="mykey=mydata"
```

# Using filter and output

```sh
POD=$(kubectl get pod -l app=rng,pod-template-hash -o name)
```

# Events
```sh
kubectl get events -w
```

# Configmaps

```sh
kubectl create configmap haproxy --from-file=haproxy.cfg
kubectl get configmap haproxy -o yaml
kubectl get configmap haproxy -o json
kubectl create configmap registry --from-literal=http.addr=0.0.0.0:80
kubectl get configmap registry
kubectl get configmap registry -o yaml
```

# Ingress

```sh
kubectl get ingress
kubectl describe ingress cheddar
kubectl get ingress/stilton -o yaml
```

# Cronjobs
```sh
kubectl create cronjob every3minutes --image alpine --schedule="*/3 * * * * " --restart=OnFailure -- sleep 10
kubectl get cronjobs
```

# kubectl run and exec

```
kubectl run pingpong --image alpine ping 1.1.1.1
kubectl run busybox --image=busybox:1.28 --command -- sleep 3600
```

## exec
Executing some arbitrary command against a running container and retrieving the output
```
kubectl exec -ti $POD_NAME -- nslookup kubernetes
kubectl exec -ti $POD_NAME -- nginx -v\n
```

Opening a bash shell
```
kubectl exec -it $YOURPOD bash
```


# kubectl port-forward
```
kubectl port-forward $POD_NAME 8181:80
kubectl port-forward hello-kubernetes-7f65c7597f-7xmn8 8181:8080 --kubeconfig=kubeconfig-prod
e.g. navigate to localhost:8181
```

