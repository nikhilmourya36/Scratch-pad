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


'''bash
user@59bce2a5f41b:~$ k get deployment 
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
blue-app    1/1     1            1           26m
green-app   1/1     1            1           12m
user@59bce2a5f41b:~$ k get deployment -o yaml 
apiVersion: v1
items:
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    annotations:
      deployment.kubernetes.io/revision: "1"
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"blue-app","namespace":"default"},"spec":{"replicas":1,"selector":{"matchLabels":{"app":"blue-app"}},"template":{"metadata":{"labels":{"app":"blue-app"}},"spec":{"containers":[{"image":"nginx","name":"nginx","ports":[{"containerPort":80}],"volumeMounts":[{"mountPath":"/usr/share/nginx/html","name":"html-volume"}]}],"initContainers":[{"command":["sh","-c","mkdir -p /work/blue \u0026\u0026 echo 'I am Blue' \u003e /work/blue/index.html"],"image":"busybox","name":"setup-html","volumeMounts":[{"mountPath":"/work","name":"html-volume"}]}],"volumes":[{"emptyDir":{},"name":"html-volume"}]}}}}
    creationTimestamp: "2026-04-10T08:04:40Z"
    generation: 1
    name: blue-app
    namespace: default
    resourceVersion: "1137"
    uid: a7aaf85f-e752-4b15-a241-198061c99be8
  spec:
    progressDeadlineSeconds: 600
    replicas: 1
    revisionHistoryLimit: 10
    selector:
      matchLabels:
        app: blue-app
    strategy:
      rollingUpdate:
        maxSurge: 25%
        maxUnavailable: 25%
      type: RollingUpdate
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: blue-app
      spec:
        containers:
        - image: nginx
          imagePullPolicy: Always
          name: nginx
          ports:
          - containerPort: 80
            protocol: TCP
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          volumeMounts:
          - mountPath: /usr/share/nginx/html
            name: html-volume
        dnsPolicy: ClusterFirst
        initContainers:
        - command:
          - sh
          - -c
          - mkdir -p /work/blue && echo 'I am Blue' > /work/blue/index.html
          image: busybox
          imagePullPolicy: Always
          name: setup-html
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          volumeMounts:
          - mountPath: /work
            name: html-volume
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
        volumes:
        - emptyDir: {}
          name: html-volume
  status:
    availableReplicas: 1
    conditions:
    - lastTransitionTime: "2026-04-10T08:04:47Z"
      lastUpdateTime: "2026-04-10T08:04:47Z"
      message: Deployment has minimum availability.
      reason: MinimumReplicasAvailable
      status: "True"
      type: Available
    - lastTransitionTime: "2026-04-10T08:04:40Z"
      lastUpdateTime: "2026-04-10T08:04:47Z"
      message: ReplicaSet "blue-app-675c456479" has successfully progressed.
      reason: NewReplicaSetAvailable
      status: "True"
      type: Progressing
    observedGeneration: 1
    readyReplicas: 1
    replicas: 1
    updatedReplicas: 1
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    annotations:
      deployment.kubernetes.io/revision: "1"
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"green-app","namespace":"default"},"spec":{"replicas":1,"selector":{"matchLabels":{"app":"green-app"}},"template":{"metadata":{"labels":{"app":"green-app"}},"spec":{"containers":[{"image":"nginx","name":"nginx","ports":[{"containerPort":80}],"volumeMounts":[{"mountPath":"/usr/share/nginx/html","name":"html-volume"}]}],"initContainers":[{"command":["sh","-c","mkdir -p /work/green \u0026\u0026 echo 'I am Green' \u003e /work/green/index.htm"],"image":"busybox","name":"setup-html","volumeMounts":[{"mountPath":"/work","name":"html-volume"}]}],"volumes":[{"emptyDir":{},"name":"html-volume"}]}}}}
    creationTimestamp: "2026-04-10T08:18:07Z"
    generation: 1
    name: green-app
    namespace: default
    resourceVersion: "1407"
    uid: a4f7a713-49de-4232-80ac-6fdcfa423f30
  spec:
    progressDeadlineSeconds: 600
    replicas: 1
    revisionHistoryLimit: 10
    selector:
      matchLabels:
        app: green-app
    strategy:
      rollingUpdate:
        maxSurge: 25%
        maxUnavailable: 25%
      type: RollingUpdate
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: green-app
      spec:
        containers:
        - image: nginx
          imagePullPolicy: Always
          name: nginx
          ports:
          - containerPort: 80
            protocol: TCP
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          volumeMounts:
          - mountPath: /usr/share/nginx/html
            name: html-volume
        dnsPolicy: ClusterFirst
        initContainers:
        - command:
          - sh
          - -c
          - mkdir -p /work/green && echo 'I am Green' > /work/green/index.htm
          image: busybox
          imagePullPolicy: Always
          name: setup-html
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          volumeMounts:
          - mountPath: /work
            name: html-volume
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
        volumes:
        - emptyDir: {}
          name: html-volume
  status:
    availableReplicas: 1
    conditions:
    - lastTransitionTime: "2026-04-10T08:18:12Z"
      lastUpdateTime: "2026-04-10T08:18:12Z"
      message: Deployment has minimum availability.
      reason: MinimumReplicasAvailable
      status: "True"
      type: Available
    - lastTransitionTime: "2026-04-10T08:18:07Z"
      lastUpdateTime: "2026-04-10T08:18:12Z"
      message: ReplicaSet "green-app-55f994c4c7" has successfully progressed.
      reason: NewReplicaSetAvailable
      status: "True"
      type: Progressing
    observedGeneration: 1
    readyReplicas: 1
    replicas: 1
    updatedReplicas: 1
kind: List
metadata:
  resourceVersion: ""
user@59bce2a5f41b:~$ k get service -o yaml 
apiVersion: v1
items:
- apiVersion: v1
  kind: Service
  metadata:
    creationTimestamp: "2026-04-10T07:40:55Z"
    labels:
      component: apiserver
      provider: kubernetes
    name: kubernetes
    namespace: default
    resourceVersion: "195"
    uid: f7d12022-b6da-4c02-a07d-f4338eb3aceb
  spec:
    clusterIP: 10.43.0.1
    clusterIPs:
    - 10.43.0.1
    internalTrafficPolicy: Cluster
    ipFamilies:
    - IPv4
    ipFamilyPolicy: SingleStack
    ports:
    - name: https
      port: 443
      protocol: TCP
      targetPort: 6443
    sessionAffinity: None
    type: ClusterIP
  status:
    loadBalancer: {}
- apiVersion: v1
  kind: Service
  metadata:
    creationTimestamp: "2026-04-10T08:11:46Z"
    name: blue-service
    namespace: default
    resourceVersion: "1266"
    uid: 5d08ac4a-5788-4d19-9307-3327e7d8b901
  spec:
    clusterIP: 10.43.134.44
    clusterIPs:
    - 10.43.134.44
    internalTrafficPolicy: Cluster
    ipFamilies:
    - IPv4
    ipFamilyPolicy: SingleStack
    ports:
    - port: 80
      protocol: TCP
      targetPort: 80
    selector:
      app: blue-app
    sessionAffinity: None
    type: ClusterIP
  status:
    loadBalancer: {}
- apiVersion: v1
  kind: Service
  metadata:
    creationTimestamp: "2026-04-10T08:18:58Z"
    name: green-service
    namespace: default
    resourceVersion: "1423"
    uid: 805562dd-34b1-45db-ae13-a675b51bcf92
  spec:
    clusterIP: 10.43.192.138
    clusterIPs:
    - 10.43.192.138
    internalTrafficPolicy: Cluster
    ipFamilies:
    - IPv4
    ipFamilyPolicy: SingleStack
    ports:
    - port: 80
      protocol: TCP
      targetPort: 80
    selector:
      app: green-app
    sessionAffinity: None
    type: ClusterIP
  status:
    loadBalancer: {}
kind: List
metadata:
  resourceVersion: ""
user@59bce2a5f41b:~$ k get ingress -o yaml 
apiVersion: v1
items:
- apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    creationTimestamp: "2026-04-10T08:23:40Z"
    generation: 1
    name: fanout-ingress
    namespace: default
    resourceVersion: "1511"
    uid: 5dc855b9-86aa-499e-8622-e51cf0d7341c
  spec:
    ingressClassName: traefik
    rules:
    - http:
        paths:
        - backend:
            service:
              name: blue-service
              port:
                number: 80
          path: /blue
          pathType: Exact
        - backend:
            service:
              name: green-service
              port:
                number: 80
          path: /green
          pathType: Exact
  status:
    loadBalancer:
      ingress:
      - ip: 172.20.0.2
kind: List
metadata:
  resourceVersion: ""
user@59bce2a5f41b:~$ curl -l http://172.20.0.2/green
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx/1.29.8</center>
</body>
</html>
user@59bce2a5f41b:~$ curl -L http://172.20.0.2/blue 
404 page not found
user@59bce2a5f41b:~$ curl -l http://172.20.0.2/blue 
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx/1.29.8</center>
</body>
</html>
user@59bce2a5f41b:~$ ^C
user@59bce2a5f41b:~$ 
'''
