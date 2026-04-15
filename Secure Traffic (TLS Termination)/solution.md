```bash
sh -c 'echo "Secure Billing Portal" > /usr/share/nginx/html/index.html && nginx -g "daemon off;"'
```

```bash
kubectl create deployment billing-app --image=nginx:alpine -- sh -c 'echo "Secure Billing Portal" > /usr/share/nginx/html/index.html && nginx -g "daemon off;"'
```

```bash
kubectl expose deployment billing-app --name=billing-service --port=80 --target-port=80
```

```bash
# Generate certs
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=billing.company.com"
```
