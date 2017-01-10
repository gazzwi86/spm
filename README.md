# SPM Docker instance

## What is it?
The intention with this repository is to create a persitent docker instance for the WordPress based SPM website.  It uses a shared MySQL instance and allows for easy distribution to a cluster on Google Cloud.

## Spec
WordPress (latest)
MySQL (5.7)

## Setup
### Ensure the 'ic-network' exists, else create it
```
docker networks ls
docker network create ic-network
```

### Bring up the container in detached mode
```
docker-compose up -d
```

## Creating production
*First time:*

```
gcloud auth application-default login
gcloud projects list
gcloud config set project <some_project>
gcloud config set compute/region europe-west1
gcloud config set compute/zone europe-west1-b
```

*Required:*

```
gcloud container clusters create spm --num-nodes 3 --zone europe-west1-b --machine-type=f1-micro
gcloud compute disks create --size 100GB spm-disk
kubectl run spm --image=<url-to-container> --port=8080
kubectl expose deployment spm --type="LoadBalancer"
kubectl get service spm
```

### View the app
```
http://<EXTERNAL-IP>:8080
```

### get details of configured server
```
kubectl describe service spm
```

### delete it all

```
kubectl delete service spm
kubectl delete pod spm
gcloud container clusters delete spm --zone europe-west1-b
gcloud compute disks delete spm-disk
```

## TODO
[ ] Setup production
[ ] Setup WP
[ ] Setup CI/CD (Jenkins)
