
*******************************************************************
*
* Demo: Load Balancer Service
*
*
*******************************************************************

# 1. YAML: Deployment & Load Balancer Service

# Deployment
# controllers/nginx-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-app
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.7.9
        ports:
        - containerPort: 80

------------------------------------

# Service - LoadBalancer
#lb.yaml
apiVersion: v1
kind: Service	
metadata:
  name: my-service
  labels:
    app: nginx-app
spec:
  selector:
    app: nginx-app
  type: LoadBalancer
  ports:
  - nodePort: 31000
    port: 80
    targetPort: 80


*******************************************************************
2. Create & Display: Deployment & Load Balancer Service

kubectl create �f nginx-deploy.yaml
kubectl create -f lb.yaml
kubectl get pod -l app=nginx-app
kubectl get deploy -l app=nginx-app 
kubectl get service -l app=nginx-app
kubectl describe service my-service

*******************************************************************
3. Testing Load Balancer Service

# To get inside the pod
kubectl exec -it [pod-name] -- /bin/sh

# Create test HTML page
cat <<EOF > /usr/share/nginx/html/test.html
<!DOCTYPE html>
<html>
<head>
<title>Testing..</title>
</head>
<body>
<h1 style="color:rgb(90,70,250);">Hello, Kubernetes...!</h1>
<h2>Load Balancer is working successfully. Congratulations, you passed :-) </h2>
</body>
</html>
EOF
exit

# Test using load-balancer-ip
http://load-balancer-ip
http://load-balancer-ip/test.html

# Testing using nodePort
http://nodeip:nodeport
http://nodeip:nodeport/test.html


*******************************************************************
4. Cleanup

kubectl delete �f nginx-deploy.yaml
kubectl delete -f lb.yaml
kubectl get pod 
kubectl get deploy 
kubectl get service 


*******************************************************************


