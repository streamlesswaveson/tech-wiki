# Cloud Guru K8s/GCP tutorial
```
docker build -t la-container-image .
gcloud auth configure-docker
docker tag la-container-image gcr.io/creating-and-61-be681efb/la-container-image:v1
docker push gcr.io/creating-and-61-be681efb/la-container-image:v1

gcloud config set compute/zone us-central1-a
gcloud container clusters create la-gke-1 --num-nodes=4
gcloud container clusters get-credentials la-gke-1
kubectl create deployment la-greetings --image=gcr.io/creating-and-61-be681efb/la-container-image:v1 --port=80
kubectl expose deployment la-greetings --type=LoadBalancer --name=la-greetings-service --port=80 --target-port=80
kubectl get services la-greetings-service
```