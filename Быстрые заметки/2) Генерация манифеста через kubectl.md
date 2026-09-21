``` bash
kubectl create deployment reports \
  --image=tisam-reports:dev \
  --port=8080 \
  --namespace=tisam \
  --dry-run=client \
  -o yaml > dev/70-reports.yaml
  
echo --- >> dev/70-reports.yaml

kubectl create service clusterip reports \
  --tcp=8080:8080 \
  --namespace=tisam \
  --dry-run=client \
  -o yaml >> dev/70-reports.yaml
```
