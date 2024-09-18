## Launching the Kubernetes Dashboard using Cluster Role Binding

The Kubernetes Dashboard is a web-based user interface for Kubernetes clusters. It provides a graphical interface for users to manage and monitor their Kubernetes clusters and applications running within them.

The dashboard user interface is not deployed by default. To deploy it, run the following command:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```
Enter the following commands to verify if the pods, services, and deployments have been created:
```
kubectl get pods -n kubernetes-dashboard -o wide
```
```
kubectl get deployment -n kubernetes-dashboard -o wide
```
```
kubectl get svc -n kubernetes-dashboard -o wide
```
To access the service outside the cluster, edit the service type from ClusterIP to NodePort using the following command:
```
kubectl edit svc -n kubernetes-dashboard kubernetes-dashboard
```
To confirm that the service type has been changed to NodePort, use the command:
```
kubectl get svc -n kubernetes-dashboard -o wide
```
To determine the location of the pod, run the following commands:
```
kubectl get pods -n kubernetes-dashboard -o wide
```
```
kubectl get svc -n kubernetes-dashboard -o wide
```
```
kubectl get nodes -o wide
```
Change the IP and NodePort accordingly:
IP : External IP of your master node

https:// <<your worker-node-1>>:<<NodePort>>

Click on the Advanced button

Click Accept the Risk and Continue

Create a service account by running the following command, and then input the code in the master node:
```
vi ServiceAccount.yaml
```
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

Apply the YAML file with the command:
```
kubectl apply -f ServiceAccount.yaml
```

Create a yaml file for cluster role binding using below command and code:
```
vi ClusterRoleBinding.yaml
```
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cr-binding1
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
Run the below command to create cluster role binding:
```
kubectl apply -f ClusterRoleBinding.yaml
```
Retrieve the token to log in by running the following command:
```
kubectl get secret $(kubectl get serviceaccount admin-user -n kubernetes-dashboard -o jsonpath='{.secrets[0].name}') -n kubernetes-dashboard -o jsonpath='{.data.token}' | base64 --decode
```
Copy the token and paste it into the Kubernetes dashboard login screen, then click Sign in

Cleanup: To delete the Kubernetes dashboard version 2.5, use the following command in the master node:
```
kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.5.0/aio/deploy/recommended.yaml
```
By following these steps, you will be able to deploy the Kubernetes Dashboard, establish secure access, and navigate the interface to monitor and manage your Kubernetes cluster.