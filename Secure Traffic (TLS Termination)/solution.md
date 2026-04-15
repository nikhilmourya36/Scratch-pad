```bash
sh -c 'echo "Secure Billing Portal" > /usr/share/nginx/html/index.html && nginx -g "daemon off;"'
```

```bash
kubectl create deployment billing-app --image=nginx:alpine -- sh -c 'echo "Secure Billing Portal" > /usr/share/nginx/html/index.html && nginx -g "daemon off;"'
```

```bash
kubectl expose deployment billing-app --name=billing-service --port=80 --target-port=80
```
