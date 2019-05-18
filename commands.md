```

Command to start kubernetes cluster:
$kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended/kubernetes-dashboard.yaml
$kubectl proxy


$ kubectl get cs
$ kubectl cluster-info
$ kubectl get nodes

$ kubectl run demo-nginx --image=nginx --replicas=2 --port=80
$ kubectl get pods
$ kubectl get deployment
$ kubectl expose deployment demo-nginx --port=80 --type=LoadBalancer
$ kubectl get service
$ kubectl describe service demo-nginx
$ kubectl delete service demo-nginx
$ kubectl expose deployment demo-nginx --type=NodePort


kubectl notes:
kubectl connects the Kubernetes API server using the RESTful API. </br>
By default, it attempts to access the localhost if .kube/config is not configured,</br>
otherwise you need to specify the API server address using the --server parameter.</br>

kubectl [command] [TYPE] [NAME] [flags]
command: Specifies the operation that you want to perform on one or more resources. 
TYPE: Specifies the resource type. Resource types are case-sensitive and you can specify the singular, plural, or abbreviated forms. 
NAME: Specifies the name of the resource. Names are case-sensitive. If the name is omitted, details for all resources are displayed. 
flags: Specifies optional flags. 

```
