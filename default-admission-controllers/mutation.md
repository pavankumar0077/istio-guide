##   ISTIO will modify the yaml file by MUTATION AND VALIDATION CONCEPTS 

![image](https://github.com/pavankumar0077/istio-guide/assets/40380941/2c73e990-0635-4b8a-9640-4f91f9ac12ee)


BEFORE MUTATION 
--
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi
  selector:
    matchLabels:
      release: "stable"
    matchExpressions:
      - {key: environment, operator: In, values: [dev]}
```

AFTER MUTATION
--
```
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 8Gi
  selector:
    matchExpressions:
    - key: environment
      operator: In
      values:
      - dev
    matchLabels:
      release: stable
  storageClassName: standard
  volumeMode: Filesystem
  volumeName: pvc-592e2832-3a1f-4c7c-888a-9b53a271a42f
status:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 8Gi
  phase: Bound
```
