*******************************************************************
.
. Demo: emptyDir
.
*******************************************************************
1. Pod with emptyDir Volume YAML (example)

# nginx-emptydir.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-emptydir
spec:
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: test-vol
      mountPath: /test-mnt
  volumes:
  - name: test-vol
    emptyDir: {}

*******************************************************************
2. Create & Display Pod with emptyDir volume

kubectl create -f nginx-emptydir.yaml
kubectl get po -o wide
kubectl exec nginx-emptydir df /test-mnt
kubectl describe pod nginx-emptydir

*******************************************************************
3. Cleanup

kubectl delete po nginx-emptydir

*******************************************************************
