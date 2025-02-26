*******************************************************************
*
* Demo: Dynamic Volume Provisioning
*
*
*******************************************************************
1. Create Storage Classes

YAML:
~~~~~~~
# sc.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: fast
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd

Create & Display:
~~~~~~~~~~~~~~~~~~
kubectl get sc
kubectl create -f sc.yaml
kubectl get sc
kubectl describe storageclass fast

*******************************************************************
2. Create Persistent Volume Claim (PVC)

# pvc1.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: my-disk-claim-1
spec:
  resources:
    requests:
      storage: 30Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: fast
  
-------------------------

# pvc2.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: my-disk-claim-2
spec:
  resources:
    requests:
      storage: 40Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: fast
  

Create & Display:
~~~~~~~~~~~~~~~~~
kubectl create -f pvc1.yaml
kubectl create -f pvc2.yaml
kubectl get pvc

*******************************************************************
3. Reference Claim inside Pod

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
      claimName: my-disk-claim-1

Create & Display:
~~~~~~~~~~~~~~~~
kubectl create -f nginx-pv.yaml
kubectl get po -o wide

*******************************************************************
4. Testing

a. Create a sample test file inside the mount.

kubectl exec pv-pod -it -- /bin/sh
df /test-pd
cd /test-pd 
echo "From test-1" > test1.txt            
exit

-------------------------------------------------------------
b. Delete the Pod

kubectl delete -f nginx-pv.yaml
kubectl get po �o wide

-------------------------------------------------------------
c. Recreate the Pod with same configuration

kubectl create -f nginx-pv.yaml
kubectl get po �o wide

-------------------------------------------------------------
d. Verify the data created in step-1 is still available?

kubectl exec pv-pod df /test-pd
kubectl exec pv-pod ls /test-pd/
kubectl exec pv-pod cat /test-pd/test1.txt

*******************************************************************
5.Cleanup
	
kubectl delete -f nginx-pv.yaml
kubectl delete -f pvc1.yaml
kubectl delete -f pvc2.yaml
kubectl delete -f sc.yaml
kubectl get pods
kubectl get pvc
kubectl get sc

******************************************************************* 
