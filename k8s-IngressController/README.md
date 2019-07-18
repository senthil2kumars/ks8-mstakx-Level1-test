# Installing the Ingress Controller

## Prerequisites

Make sure you have kubernetes cluster access to install the Ingress controller.

* For NGINX Ingress controller, use the image `nginx/nginx-ingress` from [DockerHub](https://hub.docker.com/r/nginx/nginx-ingress/).

## 1. Create a Namespace, a SA, the Default Secret, the Customization Config Map, and Custom Resource Definitions

1. Create a namespace and a service account for the Ingress controller:
    ```
    kubectl apply -f common/ns-and-sa.yaml
    namespace/nginx-ingress created
    serviceaccount/nginx-ingress created
    ```

2. Create a secret with a TLS certificate and a key for the default server in NGINX:
    ```
    $ kubectl apply -f common/default-server-secret.yaml
    secret/default-server-secret created
    ```

    **Note**: The default server returns the Not Found page with the 404 status code for all requests for domains for which there are no Ingress rules defined. For testing purposes we include a self-signed certificate and key that we generated. However, we recommend that you use your own certificate and key.

3. Create a config map for customizing NGINX configuration (read more about customization [here](configmap-and-annotations.md)):
    ```
    $ kubectl apply -f common/nginx-config.yaml
    configmap/nginx-config created
    ```

## 2. Configure RBAC

If RBAC is enabled in your cluster, create a cluster role and bind it to the service account, created in Step 1:
```
$ kubectl apply -f rbac/rbac.yaml
clusterrole.rbac.authorization.k8s.io/nginx-ingress created
clusterrolebinding.rbac.authorization.k8s.io/nginx-ingress created
```

**Note**: To perform this step you must be a cluster admin. Follow the documentation of your Kubernetes platform to configure the admin access. For GKE, see the [Role-Based Access Control](https://cloud.google.com/kubernetes-engine/docs/how-to/role-based-access-control) doc.

## 3. Deploy the Ingress Controller

We include two options for deploying the Ingress controller:
* *Deployment*. Use a Deployment if you plan to dynamically change the number of Ingress controller replicas.
* *DaemonSet*. Use a DaemonSet for deploying the Ingress controller on every node or a subset of nodes.

### 3.1 Create a Deployment

For NGINX, run:
```
$ kubectl apply -f deployment/nginx-ingress.yaml
deployment.apps/nginx-ingress created
$kubectl get deployment -n nginx-ingress
NAME            READY   UP-TO-DATE   AVAILABLE   AGE
nginx-ingress   1/1     1            1           72s

```

Kubernetes will create one Ingress controller pod.


### 3.2 Create a DaemonSet

For NGINX, run:
```
$kubectl apply -f daemon-set/nginx-ingress.yaml
daemonset.apps/nginx-ingress created
kubectl get daemonset -n nginx-ingress
NAME            DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
nginx-ingress   2         2         2       2            2           <none>          24s
```

Kubernetes will create an Ingress controller pod on every node of the cluster. Read [this doc](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) to learn how to run the Ingress controller on a subset of nodes, instead of every node of the cluster.

### 3.3 Check that the Ingress Controller is Running

Run the following command to make sure that the Ingress controller pods are running:
```
$ kubectl get pods --namespace=nginx-ingress
```

## 4. Get Access to the Ingress Controller

**If you created a daemonset**, ports 80 and 443 of the Ingress controller container are mapped to the same ports of the node where the container is running. To access the Ingress controller, use those ports and an IP address of any node of the cluster where the Ingress controller is running.

**If you created a deployment**, below are two options for accessing the Ingress controller pods.

### 4.1 Service with the Type NodePort

Create a service with the type *NodePort*:
```
$ kubectl create -f service/nodeport.yaml
service/nginx-ingress created
```
Kubernetes will randomly allocate two ports on every node of the cluster. To access the Ingress controller, use an IP address of any node of the cluster along with the two allocated ports. Read more about the type NodePort [here](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport).


## 5. Access the Live Activity Monitoring Dashboard / Stub_status Page
For NGINX, you can access the [stub_status page](http://nginx.org/en/docs/http/ngx_http_stub_status_module.html):
1. Stub_status is enabled by default. Ensure that the `nginx-status` command-line argument is not set to false.
1. Stub_status is available on port 8080 by default. It is customizable by the `nginx-status-port` command-line argument. If yours is not on 8080, modify the kubectl proxy command below.
1. Use the `kubectl port-forward` command to forward connections to port 8080 on your local machine to port 8080 of an NGINX Ingress controller pod (replace `<nginx-ingress-pod>` with the actual name of a pod):.
    ```
    $ kubectl port-forward <nginx-ingress-pod> 8080:8080 --namespace=nginx-ingress
    ```
Open your browser at http://127.0.0.1:8080/stub_status to access the status.


## Uninstall the Ingress Controller

Delete the `nginx-ingress` namespace to uninstall the Ingress controller along with all the auxiliary resources that were created:
```
$ kubectl delete namespace nginx-ingress
```
