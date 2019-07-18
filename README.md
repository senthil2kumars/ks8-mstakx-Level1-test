# ks8-mstakx-Level1-test



Tasks:

1.	Create a Kubernetes cluster on GCP (GCP gives free credits on signup so those should suffice for this exercise). If possible, share a script / code which can be used to create the cluster.

Solution:
   I have created a single control-plane cluster with kubeadm in AWS cloud platform.
   See the [kubernetes installation](https://github.com/senthil2kumars/ks8-mstakx-Level1-test/tree/master/k8s-install) for details about creating a cluster.

2.	Install nginx ingress controller on the cluster. For now, we consider that the user will add public IP of ingress LoadBalancer to their /etc/hosts file for all hostnames to be used. So do not worry about DNS resolution.
https://github.com/senthil2kumars/k8s-IngressController

Solution:
   I have Installed nginx ingress controller on the cluster.
   See the [Nginx ingress Controller installation](https://github.com/senthil2kumars/ks8-mstakx-Level1-test/tree/master/k8s-IngressController) for details about nginx ingress controller creation on the cluster.

3.	On this cluster, create namespaces called staging and production. 
   Install guest-book application on both namespaces.
	Expose staging application on hostname staging-guestbook.mstakx.io
   Expose production application on hostname guestbook.mstakx.io
   Implement a pod autoscaler on both namespaces which will scale frontend pod replicas up and down based on CPU utilization of pods. 

Solution:
   I have installed Guestbook application and exposed hostname as required.
   See the [Gustbook application installation](https://github.com/senthil2kumars/ks8-mstakx-Level1-test/tree/master/guestbook-deploy) for details about nginx ingress controller creation on the cluster.
   
4.	Write a script which will demonstrate how the pods are scaling up and down by increasing/decreasing load on existing pods.
https://github.com/senthil2kumars/guestbook-deploy
   Write a wrapper script which does all the steps above. Mention any pre-requisites in the README.md at the root of your repo.

Solution:
   I have created job to demonstrate the pod scaling up and down by increasing on existing pod.
   Please refer the [HPA](https://github.com/senthil2kumars/ks8-mstakx-Level1-test/blob/master/guestbook-deploy/README.md)
   
