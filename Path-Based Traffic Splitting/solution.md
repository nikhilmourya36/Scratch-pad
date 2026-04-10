```yaml
# blue-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue-app
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: blue-app
  template:
    metadata:
      labels:
        app: blue-app
    spec:
      # --- Init container creates the HTML file before Nginx starts ---
      initContainers:
        - name: setup-html
          image: busybox
          # Creates the /blue/ directory and writes the index.html file
          command: ["sh", "-c", "mkdir -p /work/blue && echo 'I am Blue' > /work/blue/index.html"]
          volumeMounts:
            - name: html-volume
              mountPath: /work          # writes into the shared volume
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
          volumeMounts:
            - name: html-volume
              mountPath: /usr/share/nginx/html  # Nginx serves from here
      # --- emptyDir volume shared between init and main container ---
      volumes:
        - name: html-volume
          emptyDir: {}
```

```yaml
kubectl expose deployment blue-app --name=blue-service --port=80 --target-port=80
```

```yaml
kubectl create ingress fanout-ingress --rule="/blue=blue-service:80" --rule="/green=green-service:80" 
```
