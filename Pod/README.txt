*******************************************************************
.
. Demo: POD
.

*******************************************************************

# 1. https://labs.play-with-k8s.com/

# nginx-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
    tier: dev
spec:
  containers:
  - name: nginx-container
    image: nginx

*******************************************************************

2. Create and display Pods

# Create and display PODs
kubectl create -f nginx-pod.yaml
kubectl get pod
kubectl get pod -o wide
kubectl get pod nginx-pod -o yaml
kubectl describe pod nginx-pod


*******************************************************************

3. Test & Delete

# To get inside the pod
kubectl exec -it nginx-pod -- /bin/sh

# Create test HTML page
cat <<EOF > /usr/share/nginx/html/test.html
<!DOCTYPE html>
<html>
<head>
<title>Testing..</title>
</head>
<body>
<h1 style="color:rgb(90,70,250);">Hello, Kubernetes...!</h1>
<h2>Congratulations, you passed :-) </h2>
</body>
</html>
EOF
exit

# Expose PODS using NodePort service
kubectl expose pod nginx-pod --type=NodePort --port=80

# Display Service and find NodePort
kubectl describe svc nginx-pod

# Open Web-browser and access webapge using 
http://nodeip:nodeport/test.html

# Delete pod & svc
kubectl delete svc nginx-pod
kubectl delete pod nginx-pod


*******************************************************************
