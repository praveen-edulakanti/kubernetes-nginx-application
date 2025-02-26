
*******************************************************************
*
* Demo: NodePort Service  |  Srinath Challa
*
*
*******************************************************************

1. Deployment & NodePort service manifest file

Deployment YAML file:
~~~~~~~~~~~~~~~~~~~~~
# Deployment
# nginx-deploy-np.yaml
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

--------------------------------------

NodePort Service YAML file:
~~~~~~~~~~~~~~~~~~~~~~~~~~
# Service
# nginx-svc-np.yaml
apiVersion: v1
kind: Service	
metadata:
  name: my-service
  labels:
    app: nginx-app
spec:
  selector:
    app: nginx-app
  type: NodePort
  ports:
  - nodePort: 31111
    port: 80
    targetPort: 80

*******************************************************************
2. Create and Display Deployment and NodePort

kubectl create �f nginx-deploy-np.yaml
kubectl create -f nginx-svc-np.yaml
kubectl get service -l app=nginx-app
kubectl get po -o wide
kubectl describe svc my-service

*******************************************************************
3. Testing

# To get inside the pod
kubectl exec [POD-IP] -it /bin/sh

# Create test HTML page
cat <<EOF > /usr/share/nginx/html/test.html
<!DOCTYPE html>
<html>
<head>
<title>Testing..</title>
</head>
<body>
<h1 style="color:rgb(90,70,250);">Hello, NodePort Service...!</h1>
<h2>Congratulations, you passed :-) </h2>
</body>
</html>
EOF
exit

Test using Pod IP:
~~~~~~~~~~~~~~~~~~~~~~~
kubectl get po -o wide
curl http://[POD-IP]/test.html
NodePort  �  Accessing using Service IP

Test using Service IP:
~~~~~~~~~~~~~~~~~~~~~~~~~~~
kubectl get svc -l app=nginx-app
curl http://[cluster-ip]/test.html

Test using Node IP (external IP)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
http://nodep-ip:nodePort/test.html
note: node-ip is the external ip address of a node.


*******************************************************************
4. Cleanup

kubectl delete -f nginx-deploy-np.yaml
kubectl delete -f nginx-svc-np.yaml
kubectl get deploy
kubectl get svc
kubectl get pods

*******************************************************************





 