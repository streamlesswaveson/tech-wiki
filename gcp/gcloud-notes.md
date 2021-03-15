# Bootstrap
```
gcloud cheat-sheet 
gcloud init
gcloud version
```

# Projects

``` 
gcloud projects list
```

# Config
```
gcloud help config
gcloud config set compute/region us-west1
gcloud config set compute/zone us-west1-c
gcloud config
gcloud config list
gcloud config configurations list

```

Switch projects
```
gcloud config set project <project-name>
```

# Networks
```
gcloud compute networks create kubernetes-the-hard-way --subnet-mode custom
gcloud compute networks subnets create kubernetes \\n--network kubernetes-the-hard-way \\n--range 10.240.0.0/24
```

## Firewall rules
```
gcloud compute firewall-rules create kubernetes-the-hard-way-allow-internal \\n--allow tcp,udp,icmp \\n--network kubernetes-the-hard-way \\n--source-ranges 10.240.0.0/24,10.200.0.0/16
gcloud compute firewall-rules create kubernetes-the-hard-way-allow-external \\n--allow tcp:22,tcp:6443,icmp \\n--network kubernetes-the-hard-way \\n--source-ranges 0.0.0.0/0
gcloud compute firewall-rules list --filter="network:kubernetes-the-hard-way"
gcloud compute firewall-rules list --filter="network:kubernetes-the-hard-way" --format=yaml

gcloud compute firewall-rules create kubernetes-the-hard-way-allow-health-check \\n    --network kubernetes-the-hard-way \\n    --source-ranges 209.85.152.0/22,209.85.204.0/22,35.191.0.0/16 \\n    --allow tcp
```

## Static addressses
```
gcloud compute addresses create kubernetes-the-hard-way \\n--region $(gcloud config get-value compute/region)
gcloud compute addresses list  --filter="name=('kubernetes-the-hard-way')"

gcloud compute addresses describe kubernetes-the-hard-way
```

# Compute instances
```
gcloud compute instances lists --filter="tags.items=kubernetes-the-hard-way"
gcloud compute instances list\ --filter="tags.items=kubernetes-the-hard-way"
gcloud compute instances list --filter="tags.items=kubernetes-the-hard-way"
```

# Kubernetes Clusters

## Create a default cluster
``` 
gcloud container clusters create learnk8s-cluster
```

## Update cluster, add auto-scaling
``` 
gcloud container clusters update learnk8s-cluster \
  --enable-autoscaling \
  --node-pool default-pool \
  --min-nodes 3 \
  --max-nodes 5
```

## Delete a cluster

## Other 
``` 
gcloud container clusters list
gcloud container clusters describe learnk8s-cluster
```

Adding context entry 
``` 
gcloud container clusters get-credentials your-cluster-name
```

## Logging into instance
```
gcloud compute ssh your-instance-name
gcloud compute ssh controller-0 \\n  --command "kubectl get nodes --kubeconfig admin.kubeconfig"

```
## Create instance

```
gcloud compute instances create your-instance-name\
    --async \
    --boot-disk-size 200GB \
    --can-ip-forward \
    --image-family ubuntu-2004-lts \
    --image-project ubuntu-os-cloud \
    --machine-type e2-standard-2 \
    --private-network-ip 10.240.0.10 \
    --scopes compute-rw,storage-ro,service-management,service-control,logging-write,monitoring \
    --subnet thesubnet \
    --tags sometag,anothertag
```
    
## Scp file into instance
```
gcloud compute scp file-1.txt file-2.txt your-instance-name:~/
```

# Parking lot

```
KUBERNETES_PUBLIC_ADDRESS=$(gcloud compute addresses describe kubernetes-the-hard-way \\n  --region $(gcloud config get-value compute/region) \\n  --format 'value(address)')
```

# Load balancing
```
gcloud compute target-pools create kubernetes-target-pool \\n    --http-health-check kubernetes
gcloud compute target-pools add-instances kubernetes-target-pool \\n   --instances controller-0,controller-1,controller-2
gcloud compute forwarding-rules create kubernetes-forwarding-rule \\n    --address ${KUBERNETES_PUBLIC_ADDRESS} \\n    --ports 6443 \\n    --region $(gcloud config get-value compute/region) \\n    --target-pool kubernetes-target-pool

gcloud compute http-health-checks create kubernetes \
    --description "Kubernetes Health Check" \
    --host "kubernetes.default.svc.cluster.local" \
    --request-path "/healthz"

gcloud compute firewall-rules create kubernetes-the-hard-way-allow-health-check \
    --network kubernetes-the-hard-way \
    --source-ranges 209.85.152.0/22,209.85.204.0/22,35.191.0.0/16 \
    --allow tcp

gcloud compute target-pools create kubernetes-target-pool \
    --http-health-check kubernetes

gcloud compute target-pools add-instances kubernetes-target-pool \
   --instances controller-0,controller-1,controller-2

gcloud compute forwarding-rules create kubernetes-forwarding-rule \
    --address ${KUBERNETES_PUBLIC_ADDRESS} \
    --ports 6443 \
    --region $(gcloud config get-value compute/region) \
    --target-pool kubernetes-target-pool
```

# Cloud SQL
``` 
gcloud sql instances create <db-name> --tier=db-n1-standard-1 --region=us-central1
```


# References
[Kelsey Hightower's Kubernetes the hard way](https://github.com/kelseyhightower/kubernetes-the-hard-way)
