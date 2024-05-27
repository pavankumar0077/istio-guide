## -- CONTINUE 

## App Architecture
- REF LINK ``` https://istio.io/latest/docs/examples/bookinfo/ ```
- ![image](https://github.com/pavankumar0077/istio-guide/assets/40380941/87f03aac-7c18-44e6-9965-01ed86a09a54)


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

-- HIT API AND TEST
```
idrbt@idrbt:~/istio-1.22.0$ kubectl get svc
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
details       ClusterIP   10.106.174.37    <none>        9080/TCP   19h
kubernetes    ClusterIP   10.96.0.1        <none>        443/TCP    41h
productpage   ClusterIP   10.107.202.40    <none>        9080/TCP   19h
ratings       ClusterIP   10.97.233.96     <none>        9080/TCP   19h
reviews       ClusterIP   10.104.102.236   <none>        9080/TCP   19h
idrbt@idrbt:~/istio-1.22.0$ minikube ssh
docker@minikube:~$ curl http://10.107.202.40:9080
<!DOCTYPE html>
<html>
  <head>
    <title>Simple Bookstore App</title>
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1">

<!-- Latest compiled and minified CSS -->
<link rel="stylesheet" href="static/bootstrap/css/bootstrap.min.css">

<!-- Optional theme -->
<link rel="stylesheet" href="static/bootstrap/css/bootstrap-theme.min.css">

  </head>
  <body>
    
    
<p>
    <h3>Hello! This is a simple bookstore application consisting of three services as shown below</h3>
</p>

<table class="table table-condensed table-bordered table-hover"><tr><th>name</th><td>http://details:9080</td></tr><tr><th>endpoint</th><td>details</td></tr><tr><th>children</th><td><table class="table table-condensed table-bordered table-hover"><thead><tr><th>name</th><th>endpoint</th><th>children</th></tr></thead><tbody><tr><td>http://details:9080</td><td>details</td><td></td></tr><tr><td>http://reviews:9080</td><td>reviews</td><td><table class="table table-condensed table-bordered table-hover"><thead><tr><th>name</th><th>endpoint</th><th>children</th></tr></thead><tbody><tr><td>http://ratings:9080</td><td>ratings</td><td></td></tr></tbody></table></td></tr></tbody></table></td></tr></table>

<p>
    <h4>Click on one of the links below to auto generate a request to the backend as a real user or a tester
    </h4>
</p>
<p><a href="/productpage?u=normal">Normal user</a></p>
<p><a href="/productpage?u=test">Test user</a></p>


    
<!-- Latest compiled and minified JavaScript -->
<script src="static/jquery.min.js"></script>

<!-- Latest compiled and minified JavaScript -->
<script src="static/bootstrap/js/bootstrap.min.js"></script>

  </body>
</html>

docker@minikube:~$ curl http://10.107.202.40:9080/api/v1/products
[{"id": 0, "title": "The Comedy of Errors", "descriptionHtml": "<a href=\"https://en.wikipedia.org/wiki/The_Comedy_of_Errors\">Wikipedia Summary</a>: The Comedy of Errors is one of <b>William Shakespeare's</b> early plays. It is his shortest and one of his most farcical comedies, with a major part of the humour coming from slapstick and mistaken identity, in addition to puns and word play."}]
```

### ISTIO MUTAL TLS
- BY DEFAULT ISTIO WILL TALK TO THE SERVICES, SOMEONE CAN ONLY TALK TO THE SERVICE IF THEY HAVE CERTIFICATE TO TALK WITH THEM, IN THE ABOVE WE ARE ONLY ACCESSING THE CURL COMMAND, HOW WE ARE ABLE TO ACCESS IT.
- BY DEFAULT ISTIO RUNS MUTAL TLS IN THE PERMISSIVE MODE, IT SAYS EITHER ACCESS THE SERVICE USING MUTUAL TLS OR YOU CAN ALSO ACCESS IT WITHOUT MUTUAL TLS
- WE NEED APPLY MUTUAL TLS
```

```
