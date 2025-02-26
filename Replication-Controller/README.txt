
*******************************************************************
.
. Demo: Replication Controller
.

*******************************************************************

1. Replication Controller YAML file

# nginx-rc.yaml  
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
spec:
  replicas: 3
  template:
    metadata:
      name: nginx-pod
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx-container
        image: nginx
        ports:
        - containerPort: 80
  selector:
    app: nginx-app


*******************************************************************
# 2. Create and display

kubectl create -f nginx-rc.yaml
kubectl get po -o wide
kubectl get po -l app=nginx-app
kubectl get rc nginx-rc
kubectl describe rc nginx-rc

*******************************************************************
# 3. Reschedule

kubectl get po -o wide --watch
kubectl get po -o wide
kubectl get nodes

*******************************************************************
# 4. Scaling up cluster

kubectl scale rc nginx-rc --replicas=5
kubectl get rc nginx-rc
kubectl get po -o wide

*******************************************************************
# 5. Scalling down

kubectl scale rc nginx-rc --replicas=3
kubectl get rc nginx-rc
kubectl get po -o wide

*******************************************************************
# 6. Cleanup

kubectl delete -f nginx-rc.yaml
kubectl get rc
kubectl get po -l app=nginx-app

*******************************************************************
