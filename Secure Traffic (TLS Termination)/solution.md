```bash
sh -c 'echo "Secure Billing Portal" > /usr/share/nginx/html/index.html && nginx -g "daemon off;"'
```

```bash
kubectl create deployment billing-app --image=nginx:alpine -- sh -c 'echo "Secure Billing Portal" > /usr/share/nginx/html/index.html && nginx -g "daemon off;"'
```
