
*******************************************************************
.
. Demo: Static Persistent Volumes
.

*******************************************************************
1. Create gcePersistent Disk on Google Cloud

From Dashboard
(OR)
gcloud compute disks create --size=10GB --zone=us-central1-a my-data-disk
gcloud compute disks list

note: create gcePersistentdisk in same zone as k8 cluster is in

*******************************************************************
2. Create "Persistent Volume(PV)" and display

YAML
~~~~~
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-gce
spec:
  capacity:
    storage: 15Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: slow
  gcePersistentDisk:
    pdName: my-data-disk
    fsType: ext4

Create:
~~~~~~
kubectl create -f pv.yaml
kubectl get pv
kubectl describe pv pv-gce 

*******************************************************************
3. Create "Persistent Volume Claim(PVC)" and display

YAML:
~~~~~
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-disk-claim
spec:
  resources:
    requests:
      storage: 15Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: slow


Create & Display:
~~~~~~~~~~~~~~~~
kubectl create -f pvc.yaml
kubectl get pvc
kubectl get pv

*******************************************************************
4. Reference claim inside the Pod:

# nginx-pv.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pv-pod
spec:
  containers:
  - name: test-container
    image: nginx
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    persistentVolumeClaim:
      claimName: my-disk-claim


Create & Display:
~~~~~~~~~~~~~~~~~
kubectl create -f nginx-pv.yaml
kubectl get po -o wide

*******************************************************************
5. Testing
	
a. Create a test file inside the mount where the disk is mounted

kubectl exec pv-pod -it -- /bin/sh
df /test-pd
cd /test-pd
echo "From first pod" > test1.txt
exit

-------------------------------------------------------------
b. Delete the Pod

kubectl delete -f nginx-pv.yaml

-------------------------------------------------------------
c. Recreate the Pod with same configuration

kubectl create -f nginx-pv.yaml
kubectl get pods -o wide

-------------------------------------------------------------
d. Verify the data created in step-1 is still available

kubectl exec pv-pod df /test-pd
kubectl exec pv-pod ls /test-pd/
kubectl exec pv-pod cat /test-pd/test1.html


*******************************************************************
# 6. Cleanup

kubectl delete -f nginx-pv.yaml
kubectl delete -f pvc.yaml
kubectl delete -f pv.yaml
kubectl get pods
kubectl get pvc
kubectl get pv

*******************************************************************





 