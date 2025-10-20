


```bash
minikube start --addons=metrics-server
minikube addons enable metrics-server

# Is `metrics-server` running?
kubectl get pods -n kube-system | grep metrics-server

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
