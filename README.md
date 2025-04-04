
# Monitoring Kubernetes Cluster with Prometheus & Grafana 

## Requirements
1. Docker Desktop (on Mac & Windows) or Docker Engine (on Linux)
2. Kubectl
3. Minikube


## Install Docker 
```sh
docker --version
```

## Install Kubectl
```sh 
brew install kubectl
kubectl version --client
```

## Install & Create Minikube cluster 
```sh
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube
minikube version
```


## Start Minikube Cluster
```sh
minikube start --driver=docker
kubectl get nodes
```


## Enable Metrics-Server & Ingress on Minikube Cluster
```sh
minikube addons enable metrics-server
minikube addons enable ingress
```


## Create Namespace - monitoring
```sh
kubectl create namespace monitoring
```


## Create Nginx Configmap first (to enable stub_status - needed for deployment), 
```sh
kubectl apply -f nginx-configmap.yaml
kubectl get cm -n monitoring
```


## Create Deployment & Service for Nginx with Nginx-Prometheus Exporter Side-car Container 
```sh
kubectl apply -f nginx-deployment.yaml

kubectl get deploy -n monitoring
kubectl get svc -n monitoring
```


## Expose Nginx UI (It creates a tunnel, so run the command in a dedicated terminal)
```sh
minikube service nginx-service -n monitoring
```


## Create Prometheus ConfigMap first (to scrape nginx-prometheus-exporter)
```sh
kubectl apply -f prometheus-configmap.yaml
```


## Create Prometheus Deployment & Service 
```sh
kubectl apply -f prometheus-deployment.yaml

kubectl get deploy -n monitoring
kubectl get svc -n monitoring
```


## Expose Prometheus UI
```sh
minikube service prometheus-service -n monitoring
```


## Monitor Nginx in Prometheus

Go to Prometheus UI -> Search for "nginx_http_requests_total" -> Press ENTER to see Nginx request metrics


## Deploy Grafana for Prometheus Metrics Visualization
```sh
kubectl apply -f grafana-deployment.yaml
```


## Expose Grafana UI
```sh
minikube service grafana-service -n monitoring
```

```sh
username: admin
password: admin
```


## Configure Grafana to Use Prometheus as a Data Source

On the Grafana page,
1. Add data source 
2. Select Prometheus 
3. Enter URL: http://prometheus-service.monitoring.svc.cluster.local:9090 
4. Click save & test.
5. Click Explore View 
6. Click Metric, select "nginx_http_requests_total" in the drop down. 
7. Refresh the Nginx and Grafana pages to see some metrics and graphs. 


## Verify all the Resources Created
```sh
kubectl get all -n monitoring
```


## Clean up
```sh 
kubectl delete ns monitoring
minikube stop
minikube delete --all
```
