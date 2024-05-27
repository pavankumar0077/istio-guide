## Good to know

### VIRTUAL SERVICES && DESTINATION RULES
- IT WILL HELP IN THE TRAFFIC MANAGMENT RULES OF ISTIO
- IN OUR USECASE VIRTUAL SERVICE AND DESTINATION RULES WILL HELP US IN IMPLEMENTING CANARY MODEL OF DEPLOYMENT.

### PROBLEM STATEMENT
- AS OF NOW BOOK REVIEWS ARE GETTING REFELCTED FROM DIFFERENT DIFFERENT VERSIONS LIKE V1 TO V3
- ![image](https://github.com/pavankumar0077/istio-guide/assets/40380941/ba0c268b-02d0-4bc8-bfcd-b206d2d4224f)
- BUT NOW I WANT TO ONLY REVIEWS V1, ALL THE TIME, THEN WE WILL INTRODUCE A NEW VERSION LET'S SAY REVIEWS V2
- THEN I ONLY WANT 50% OF THE TRAFFIC TO GO TO REVIEWS V2 AND 50 % OF THE TRAFFIC GO TO REVIEWS V3
- AS OF NOW IT IS CONTINUOSLY CHANGING FROM V1 TO V3, LET'S MAKE TO TALK TO ONLY V1 INITIALLY 
- TRAFFIC SHIFTING IS NOTHING BUT CANARY MODEL

### SOLUTION :
- DEPLOY old-version.yaml file
- HERE KIND -- **VirtualService** for detail where EVERY REQUEST SHOULD GO TO THE DESTINATION DETAILS V1.
- DESTINATION -- IT IS ANOTHER CUSTOM RESOURCE WHERE WE ARE GOING TO CREATE AND IN THE DESTINATION WE WILL MENTION THE SUBSET V1 WILL ONLY POINT TO THE VERSION ONE OF DETAILS.
- SIMILARLY WHEN WE ARE TAKING ABOUT **RATINGS**  OFCOURSE ALL OF THEM HAVE VERSION 1, SO THE REVIEWS VIRTUAL SERVICE IS SAYING ANY REQUEST THAT COMES TO THE REVIEWS MICROSERVICES OR THE SIDECAR CONTAINER THEY SHOULD GO TO DESTINATIONS REVIEWS SUBSET V1, Now we have to cerate this destination and in the destination we will say REQEUST SHOULD ONLY GO THE POD WITH VERSION 1 

```
idrbt@idrbt:~/istio-1.22.0$ kubectl apply -f old-version.yaml 
virtualservice.networking.istio.io/productpage created
virtualservice.networking.istio.io/reviews created
virtualservice.networking.istio.io/ratings created
virtualservice.networking.istio.io/details created
```
- Right now only 50% done WE HAVE CREATED VIRTUAL SERVICE, without DESTINATION RULES IT IS INCOMPLETE
```
idrbt@idrbt:~/istio-1.22.0$ kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml 
destinationrule.networking.istio.io/productpage created
destinationrule.networking.istio.io/reviews created
destinationrule.networking.istio.io/ratings created
destinationrule.networking.istio.io/details created
```
```
apiVersion: networking.istio.io/v1
kind: DestinationRule
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"networking.istio.io/v1alpha3","kind":"DestinationRule","metadata":{"annotations":{},"name":"reviews","namespace":"default"},"spec":{"host":"reviews","subse>
  creationTimestamp: "2024-05-27T07:20:21Z"
  generation: 1
  name: reviews
  namespace: default
  resourceVersion: "203978"
  uid: 511a7373-18c5-41e4-bd46-e36f8aac7c42
spec:
  host: reviews
  subsets:
  - labels:
      version: v1
    name: v1
  - labels:
      version: v2
    name: v2
  - labels:
      version: v3
    name: v3
```
- IF THE SUBSET IS V1 THEN IT ALWAYS GO TO VERSION  V1, WE HAVE MENTIONED THIS IN THE VIRTUAL SERVICES

![image](https://github.com/pavankumar0077/istio-guide/assets/40380941/36a12fe1-d4d3-4539-92a3-19a448fcd285)
