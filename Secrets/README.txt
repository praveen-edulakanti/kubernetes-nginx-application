
*************************************************************************************************************************************************
.
. Demo: Secrets
.
*************************************************************************************************************************************************

Overview:
---------
1. Create Secret using "kubectl" & Consuming it from "volumes" inside Pod

   1a. Create secret "nginx-secret-vol" using "Kubectl"
   1b. Consume "nginx-secret-vol" from "volumes" inside Pod
   1c. Create | Display | Validate

2. Create Secret "manually" using YAML file & Consuming it from "environment variables" inside Pod

   2a. Create secret �redis-secret-env� using YAML file:
   2b. Consume �redis-secret-env� secret from �Environment Variables� inside pod
   2c. Create | Display | Validate

3. Cleanup

   3a. Delete secrets
   3b. Delete pods
   3c. Validate

*************************************************************************************************************************************************

# 1. Creating Secret using Kubectl & Consuming it from "volumes" inside Pod


1a. Creating secret using "Kubectl":
------------------------------------
echo -n 'admin' > username.txt
echo -n 'pa$$w00rd' > password.txt

kubectl create secret generic nginx-secret-vol --from-file=username.txt --from-file=password.txt

# rm -f username.txt password.txt

kubectl get secrets
kubectl describe secrets nginx-secret-vol

==========================================================

1b. Consuming "nginx-secret-vol" from "volumes" inside Pod
--------------------------------------------------------

#nginx-pod-secret-vol.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-secret-vol
spec:
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: test-vol
      mountPath: "/etc/confidential"
      readOnly: true
  volumes:
  - name: test-vol
    secret:
      secretName: nginx-secret-vol

==========================================================

1c. Create | Display | Validate:
--------------------------------

# Create
kubectl create -f nginx-pod-secret-vol.yaml

# Display
kubectl get po
kubectl get secrets
kubectl describe pod nginx-pod-secret-vol

# Validate from "inside" the pod
kubectl exec nginx-pod-secret-vol -it /bin/sh
cd /etc/confidential
ls 
cat username.txt
cat password.txt
exit

(OR)

# Validate from "outside" the pod
kubectl exec nginx-pod-secret-vol ls /etc/confidential
kubectl exec nginx-pod-secret-vol cat /etc/confidential/username.txt
kubectl exec nginx-pod-secret-vol cat /etc/confidential/password.txt


*************************************************************************************************************************************************

2. Creating Secret "manually" using YAML file & Consuming it from "environment variables" inside Pod


2a.  Creating Secret using YAML file:
-------------------------------------

# Encoding secret
echo -n 'admin' | base64
echo -n 'pa$$w00rd' | base64

# YAML file
# redis-secret-env.yaml
apiVersion: v1
kind: Secret
metadata:
  name: redis-secret-env
type: Opaque
data:
  username: YWRtaW4=
  password: cGEkJHcwMHJk

kubectl create -f redis-secret-env.yaml
kubectl get secret
kubectl describe secret redis-secret-env

===============================================================================

2b. Consuming �redis-secret-env� secret from �Environment Variables� inside pod
--------------------------------------------------------------------------------

# redis-pod-secret-env.yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis-pod-secret-env
spec:
  containers:
  - name: redis-container
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: redis-secret-env
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: redis-secret-env
            key: password
  restartPolicy: Never

===============================================================================

2c. Create | Display | Validate:

# Create
kubectl create -f  redis-pod-secret-env.yaml

# Display
kubectl get pods
kubectl get secrets
kubectl describe pod redis-pod-secret-env


# Validate from "inside" the pod
kubectl exec redis-pod-secret-env -it /bin/sh
env | grep  SECRET
exit

(OR)

# Validate from "outside" the pod
kubectl exec redis-pod-secret-env env | grep SECRET


*************************************************************************************************************************************************

3. Cleanup

# Delete secrets
kubectl delete secrets nginx-secret-vol redis-secret-env

# Delete pods
kubectl delete pods nginx-pod-secret-vol redis-pod-secret-env

# Validate
kubectl get pods
kubectl get secrets


*************************************************************************************************************************************************

 