## -- CONTINUE 

-- APPLY GATEWAY
```
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
```
-- INSTALL THE APPLICATION AND RUN IN LOCAL BY GATEWAY AND MINIKUBE TUNNEL
```
drbt@idrbt:~/istio-1.22.0$ minikube tunnel
[sudo] password for idrbt: 
Sorry, try again.
[sudo] password for idrbt: 
Sorry, try again.
[sudo] password for idrbt: 
Status:	
	machine: minikube
	pid: 3159479
	route: 10.96.0.0/12 -> 192.168.49.2
	minikube: Running
	services: []
    errors: 
		minikube: no errors
		router: error adding Route: sudo: 3 incorrect password attempts
, 2
		loadbalancer emulator: no errors
[sudo] password for idrbt: 
Status:	
	machine: minikube
	pid: 3159479
	route: 10.96.0.0/12 -> 192.168.49.2
	minikube: Running
	services: [istio-ingressgateway]
    errors: 
		minikube: no errors
		router: no errors
		loadbalancer emulator: no errors
^C[sudo] password for idrbt: 

```

- After deployed edit the pods

```
idrbt@idrbt:~/istio-1.22.0$ kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
details-v1-69b9bc848-vz4tk        2/2     Running   0          19h
productpage-v1-78dd566f6f-jk2tp   2/2     Running   0          19h
ratings-v1-7c9cd8db6d-p8v69       2/2     Running   0          19h
reviews-v1-8464b4668-vzb87        2/2     Running   0          19h
reviews-v2-6df9f9c888-nnnnm       2/2     Running   0          19h
reviews-v3-7dfd5d4c6d-dxbqx       2/2     Running   0          19h
idrbt@idrbt:~/istio-1.22.0$ kubectl edit pod details-v1-69b9bc848-vz4tk
Edit cancelled, no changes made.
idrbt@idrbt:~/istio-1.22.0$
```
- 1. HERE WE CAN FIND 2 CONTAINERS HERE ONE IS ACTUAL DOCKER AND OTHER ONE IS SIDE-CAR CONTAINER.
```
spec:
  containers:
  - image: docker.io/istio/examples-bookinfo-details-v1:1.19.1
    imagePullPolicy: IfNotPresent
    name: details
    ports:
    - containerPort: 9080
      protocol: TCP
```
- 2. SIDE-CAR CONTAINER
```
 - proxy
    - sidecar
    - --domain
    - $(POD_NAMESPACE).svc.cluster.local
    - --proxyLogLevel=warning
    - --proxyComponentLogLevel=misc:error
    - --log_output_level=default:info
    env:
    - name: PILOT_CERT_PROVIDER
      value: istiod
    - name: CA_ADDR
      value: istiod.istio-system.svc:15012
    - name: POD_NAME
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.name
    - name: POD_NAMESPACE
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.namespace
```

