## VALIDATION ADMISSION CONTROLLER

- WE HAVE A VALIDATION ADMISSION CONTROLLER CALLED RESOURCE QUOTA
- 1ST WE WILL CREATE A RESOURCE QUOTA OF 1GI of RAM.
- NEXT WE WILL CREATE A POD WITH 10GIB OF RAM, THEN WE WILL GET A ERROR AND THIS IS VALIDATED BY ADMISSION CONTROLLER
- RESOURCE QUOTA.

```
apiVersion: v1
kind: ResourceQuota
metadata:
  name: quota-demo
spec:
  hard:
    cpu: "1"
    memory: 2Gi
```

ERROR : VALIDATION
--
```
[~/K8S] pavan@pavan-virtualbox $ kubectl apply -f pod.yaml 
Error from server (Forbidden): error when creating "pod.yaml": pods "demo-pod" is forbidden:
exceeded quota: quota-demo, requested: memory=10Gi, used: memory=0, limited: memory=2Gi
```
