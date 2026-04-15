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
openssl req -help 2>&1 | grep -i "subject\|days\|key\|x509\|nodes"
```

```bash
# Generate certs
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=billing.company.com"
```

```bash
kubectl create secret tls billing-tls-secret --cert=tls.crt --key=tls.key
```
