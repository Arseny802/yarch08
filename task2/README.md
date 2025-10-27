# Инсрукции по запуску

### 1. Для того, чтобы запустить тесты locust в сервисе, выполните:
```bash
minikube start --addons=metrics-server
minikube addons enable metrics-server

# Is `metrics-server` running?
kubectl get pods -n kube-system | grep metrics-server

kubectl create namespace monitoring
kubectl apply -f service.yaml
kubectl apply -f deployment.yaml
kubectl apply -f hpa.yaml

# Get URL for `scaletestapp-service` and check it
curl `minikube service scaletestapp-service --url`
# Check `scaletestapp-hpa`
kubectl get hpa 

# Run locust tests 
locust -H `minikube service scaletestapp-service --url` \
  -u 1000 -t 10m --headless --logfile locust.log --json-file locust_result \
  --only-summary > locust.log 2>&1
```

### 2. Для того, чтобы получить Prometheus метрики, выполните п.1 и команды:
```bash
helm repo add prometheus-community "https://prometheus-community.github.io/helm-charts"
helm repo update
helm install prometheus-operator prometheus-community/kube-prometheus-stack --namespace monitoring

kubectl apply -f service-monitor.yaml
kubectl apply -f yarch08.prometheus.yaml
kubectl apply -f prometheus-operator.yaml

helm install prometheus-adapter prometheus-community/prometheus-adapter \
  --namespace monitoring --values values.yaml

# Wait for pod started
kubectl get --raw /apis/custom.metrics.k8s.io/v1beta1

kubectl port-forward service/scaletestapp-service 8080:8080


# Run locust tests 
locust -H `minikube service scaletestapp-service --url` \
  -u 1000 -t 10m --headless --logfile locust.log --json-file locust_result \
  --only-summary > locust.log 2>&1
```
