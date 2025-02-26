
*************************************************************************************************************************************************
*
* Demo: DaemonSet
*
*************************************************************************************************************************************************

Overview:
~~~~~~~~~~

1. Deploy Pod on "all" worker nodes inside k8s cluster using DaemonSet

   1a. fluentd-DaemonSet manifest file
   1b. Create | Display | Validate

2. Deploy Pod on "Subset" of worker nodes inside k8s cluster using DaemonSet

   2a. Attach label to the nodes 
   2b. nginx-DamoneSet manifest file review
   2c. Create | Display | Validate

3. Cleanup

*************************************************************************************************************************************************


1. Deploy Pod on "all" worker nodes inside k8s cluster using DaemonSet

1a. YAML File:
--------------
# fluentd-ds-allnodes.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-ds
spec:
  template:
    metadata:
      labels:
        name: fluentd
    spec:
      containers:
      - name: fluentd
        image: gcr.io/google-containers/fluentd-elasticsearch:1.20
  selector:
    matchLabels:
      name: fluentd

---------------------------------------------------------------------

1b. Create | Display | Validate

kubectl create -f fluentd-ds-allnodes.yaml
kubectl get po -o wide
kubectl get ds
kubectl describe ds fluentd-ds

*************************************************************************************************************************************************

2. Deploy Pod on "Subset" of worker nodes inside k8s cluster using DaemonSet

----------------------------------------

2a. Attach label to the nodes

kubectl get nodes
kubectl label nodes worker1 worker2 disktype=ssd
kubectl get nodes --show-labels

----------------------------------------

2b. YAML

# nginx-ds-subsetnodes.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-ds
spec:
  template:
    metadata:
      labels:
        name: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx
      nodeSelector:
        disktype: ssd
  selector:
    matchLabels:
      name: nginx


----------------------------------------

2c. Create | Display | Validate

kubectl create -f nginx-daemonset.yaml
kubectl get po -o wide
kubectl get ds
kubectl describe ds nginx-ds

*************************************************************************************************************************************************

3. Ceanup

kubectl delete ds fluentd-ds
kubectl delete ds nginx-ds
kubectl get po

*************************************************************************************************************************************************






 