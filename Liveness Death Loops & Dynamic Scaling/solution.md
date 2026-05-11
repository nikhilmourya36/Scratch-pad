```bash
kubectl create deployment payment-service --image=nginx:stable --port=80 --replicas=1 --dry-run=client -o yaml > payment-service.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: payment-service
  namespace: resiliency-lab
  labels:
    app: payment-service
spec:
  replicas: 1
  selector:
    matchLabels:
      app: payment-service
  template:
    metadata:
      labels:
        app: payment-service
    spec:
      containers:
      - name: nginx
        image: nginx:stable
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "50m"           # Required for HPA to compute % utilization
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - wget -q -O- http://db-service:8080   # The fatal flaw
          periodSeconds: 5
```
