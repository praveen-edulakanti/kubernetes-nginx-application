
*******************************************************************
*
* Demo: gce Persistent Disk
*
*
*******************************************************************
1. Create Disk on Google Cloud

From Dashboard
(OR)
gcloud compute disks create --size=10GB --zone=us-central1-a my-data-disk
gcloud compute disks list

note: gcloud auth login

*******************************************************************
2. Pod with gcePersistentDisk YAML file

apiVersion: v1
kind: Pod
metadata:
  name: gce-pd
spec:
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    gcePersistentDisk:
      pdName: my-data-disk
      fsType: ext4

----------------------------------------------------

Bug Fix: For deploying Pod with gcePersistentDisk on GCE "VMs"
~~~~~~

a. Manually attach above created disk to worker node through GCE dashboard

b. Format above disk on the respective worker node where the above pod deployed
mkfs.ext4 /dev/disk/by-id/scsi-0Google_PersistentDisk_{Persistent Disk Name}

c Mount it in the location expected by Kubernetes. Run the following commands as root:
mkdir -p /var/lib/kubelet/plugins/kubernetes.io/gce-pd/mounts/{Persistent Disk Name} && mount /dev/disk/by-id/scsi-0Google_PersistentDisk_{Persistent Disk Name} /var/lib/kubelet/plugins/kubernetes.io/gce-pd/mounts/{Persistent Disk Name}

Reference: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_atomic_host/7/html/getting_started_with_kubernetes/get_started_provisioning_storage_in_kubernetes

----------------------------------------------------


*******************************************************************
3. Create and Display

kubectl create -f gcepd.yaml
kubectl get po �o wide
kubectl describe po gce-pd


*******************************************************************
4. Testing: Creating file inside mount where gcePersistentDisk volume is mounted and then delete

Create test file inside pod
---------------------------
kubectl exec gce-pd -it -- /bin/sh
df
echo "Testing - 1" > /test-pd/test1.html
exit

Delete pod:
-----------
kubectl delete -f gce-pd.yaml


*******************************************************************
5. Validate: Recreate Pod using same gcePersistentDisk above and validate above data

Recreate pod with same  config
------------------------------
kubectl create -f gce-pd.yaml
kubectl get po �o wide

Validate test file created in step#4 is still available?
--------------------------------------------------------
kubectl exec gce-pd -it /bin/sh
ls /test-pd/
cat /test-pd/test1.html


*******************************************************************
6. Cleanup

kubectl delete -f gce-pd.yaml
kubectl get pods


*******************************************************************






 