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
