```
# Base64 encode CSR without newlines for CSR resource
CSR_CONTENT=$(base64 -w0 dev-user.csr)                # base64 -w0 removes line breaks for YAML embedding
```
```
# Create CertificateSigningRequest manifest
cat <<EOF > dev-user-csr.yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: dev-user-csr
spec:
  request: ${CSR_CONTENT}
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
EOF
```

```
# Approve CSR and extract signed certificate
kubectl certificate approve dev-user-csr              # approve CSR as cluster admin
```
```
# decode issued cert into dev-user.crt
kubectl get csr dev-user-csr -o jsonpath='{.status.certificate}' | base64 -d > dev-user.crt                                      
```

```
sudo kubectl config set-credentials dev-user \
  --client-certificate=/home/user/dev-user.crt \
  --client-key=/home/user/dev-user.key                # point kubeconfig user at cert/key pair
```

```
sudo kubectl config set-context dev-user-context \
  --cluster=default \
  --user=dev-user                                      # new context referencing existing cluster + dev-user
```



                                                      # Terminal History 

### 1. Generate Private Key
```bash
openssl genrsa -out private.key 2048
````

### 2. Create Certificate Signing Request (CSR)

```bash
openssl req -new -key private.key -out dev-user.csr
```

### 3. Base64 Encode the CSR

```bash
base64 -w 0 dev-user.csr > dev-user.csr.base64
```

### 4. Create Kubernetes CSR YAML

```bash
cat <<EOF > dev-user-k8s-csr.yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: dev-user-csr
spec:
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
  request: $(cat dev-user.csr.base64)
EOF
```

### 5. Submit and Approve the CSR

```bash
kubectl apply -f dev-user-k8s-csr.yaml
kubectl get csr
kubectl certificate approve dev-user-csr
```

### 6. Extract the Signed Certificate

```bash
kubectl get csr dev-user-csr -o jsonpath='{.status.certificate}' | base64 --decode > dev-user.crt
```

### 7. Configure kubeconfig Credentials

```bash
kubectl config set-credentials dev-user \
  --client-certificate=/home/user/dev-user.crt \
  --client-key=/home/user/private.key
```

### 8. Create and Switch Context

```bash
kubectl config get-clusters
kubectl config set-context dev-user-context \
  --cluster=default \
  --user=dev-user

kubectl config use-context dev-user-context
```

### 9. Apply RBAC Role and RoleBinding

```bash
kubectl apply -f role.yaml
kubectl apply -f rolebinding.yaml
```

### 10. Verify Access

```bash
kubectl get nodes
kubectl get pods



