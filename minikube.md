test in minikube

 > kubectl create deployment yazero-depl --image yazerohub/my_flask_app:latest  
 > kubectl expose deployment yazero-depl --type=ClusterIP --port 5000  
 > kubectl scale deployment yazero-depl --replicas 2  

## k8s.yml
```
---
apiVersion : apps/v1
kind: Deployment
metadata:
  name: yazero-depl-dep
  labels:
    app  : flask_in_docker
    owner: yazerohub
spec:
  replicas: 2
  selector:
    matchLabels:
      project: k8s_flask
  template:
    metadata:
      labels:
        project: k8s_flask
    spec:
      containers:
        - name : my-flask
          image: yazerohub/my_flask_app:latest
          ports:
            - containerPort: 5000


apiVersion: v1
kind: Service
metadata:
  name: yazero-depl-svc
  labels:
    env  : prod
    owner: yazerohub
spec:
  selector:
    project: k8s_flask    # Selecting PODS with those Labels
  ports:
    - name      : app-listener
      protocol  : TCP
      port      : 5000  # Port on Load Balancer
      targetPort: 5000  # Port on POD
  type: ClusterIP
```
### https://projectcontour.io/getting-started/
### установка ingress controller 
kubectl apply -f https://projectcontour.io/quickstart/contour.yaml
 
## ingress-hosts.yml
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
    name: ingress-route

spec:
   rules:
   - host: www.yazero.ru
     http:
       paths:
        - path: /
          pathType: Prefix
          backend:
            service:
               name: yazero-depl-svc
               port:
                number: 5000
```


add to /etc/nginx/nginx.conf
```
stream {
  server {
      listen ip_host_machine:81;
      #TCP traffic will be forwarded to the specified server
      proxy_pass minikube_ip:80;
  }
}
```

> on notebook C:\Windows\System32\drivers\etc\hosts
> ip  domainname


in browse www.yazero.ru:81
```
My first app Flask in Docker.
Server:www.yazero.ru
Clinet:172.17.0.10
```




access to dashboard minikube 
 > root@minikube:/#minikube dashboard  

minikube host:  
 > kubectl proxy &  

notebook:   
 > ssh -L 12345:localhost:8001 ubuntu@10.238.62.15  

link:  
 > http://localhost:12345/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/#/pod?namespace=default
