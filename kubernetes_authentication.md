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


# history 

user@4a62a981ad61:~$ history 
ls 
openssl genrsa -key -out private.key 2480
    3  openssl genrsa -key -out private.key 
    4  openssl genrsa -h
    5  openssl genrsa -help
    6  openssl genrsa -key -out private.key 
    7  openssl genrsa -key -out private.key 2480
    8  openssl -help
    9  openssl genrsa -help
   10  openssl genrsa -out private.key 2048
   11  openssl -req -help
   12  openssl req -new -newkey private.key  -out dev-user.csr -help
   13  openssl req -new -newkey private.key  -out dev-user.csr 
   14  openssl req -new  private.key  -out dev-user.csr 
   15  openssl req -new -key  private.key  -out dev-user.csr 
   16  ls
   17  base64 -w 0 dev-user.csr > dev-user.csr.base64
   18  cat <<EOF > dev-user-k8s-csr.yaml
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

   19  ls
   20  alias k='kubectl'
   21  k apply -f dev-user-k8s-csr.yaml 
   22  k get csr 
   23  k approve csr dev-user-csr
   24  k csr approve dev-user-csr
   25  k certificate approve dev-user-csr
   26  k get csr 
   27  k describe csr dev-user-csr
   28  k describe csr dev-user-csr -o json{'spec.request'}
   29  k describe --help
   30  k describe csr dev-user-csr |echo "$json{'spec.request'}"
   31  k describe csr dev-user-csr |echo "$json{spec.request}"
   32  kubectl get csr dev-user-csr -o jsonpath='{.status.certificate}'   | base64 --decode > dev-user.crt
   33  ls
   34  cat dev-user.crt
   35  ls 
   36  kubectl config set-credentials dev-user   --client-certificate=/home/user/dev-user.crt   --client-key=/home/user/private.key
   37  sudo kubectl config set-credentials dev-user   --client-certificate=/home/user/dev-user.crt   --client-key=/home/user/private.key
   38  kubectl config get-users
   39  k config get-cluster
   40  k config -h
   41  k config get-clusters
   42  kubectl config set-context dev-user-context   --cluster=default   --user=dev-user
   43  sudo kubectl config set-context dev-user-context   --cluster=default   --user=dev-user
   44  kubectl config get-contexts
   45  k config set-context dev-user-context
   46  sudo k config set-context dev-user-context
   47  sudo kubectl config set-context dev-user-context
   48  kubectl config get-contexts
   49  sudo kubectl config set-context dev-user-context
   50  kubectl config get-contexts
   51  kubectl config current-context
   52  kubectl config use-context dev-user-context
   53  sudo kubectl config use-context dev-user-context
   54  kubectl config get-contexts
   55  vi role.yaml
   56  vi rolebinding.yaml
   57  k apply -f role.yaml 
   58  sudo kubectl config use-context default
   59  k apply -f role.yaml 
   60  k apply -f rolebinding.yaml 
   61  sudo kubectl config use-context dev-user-context 
   62  k get nodes
   63  k get pod 
   64  history 


