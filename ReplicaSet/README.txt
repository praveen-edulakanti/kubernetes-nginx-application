
*******************************************************************
.
. Demo: ReplicaSet
.

*******************************************************************
ReplicaSet YAML file

# nginx-rs.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  template:
    metadata:
      name: nginx-pod
      labels:
        app: nginx-app
        tier: frontend
    spec:
      containers:
      - name: nginx-container
        image: nginx
        ports:
        - containerPort: 80
  selector:
    matchLabels:
      app: nginx-app
    matchExpressions:
      - {key: tier, operator: In, values: [frontend]}


*******************************************************************
# 2. Create and display replicaset

kubectl create -f nginx-rs.yaml
kubectl get po -o wide
kubectl get po -l app=nginx-app
kubectl get rs nginx-rs -o wide
kubectl describe rs nginx-rs

*******************************************************************
# 3. Automatic Pod Reschedule 

kubectl get po -o wide --watch
kubectl get po -o wide
kubectl get nodes

*******************************************************************
# 4. Scale up pods

kubectl scale rs nginx-rs --replicas=5
kubectl get rs nginx-rs -o wide
kubectl get po -o wide

*******************************************************************
# 5. Scale down pods

kubectl scale rs nginx-rs --replicas=3
kubectl get rs nginx-rs -o wide
kubectl get po -o wide

*******************************************************************
# 6. Cleanup

kubectl delete -f nginx-rs.yaml
kubectl get rs
kubectl get po -l app=nginx-app

*******************************************************************





 