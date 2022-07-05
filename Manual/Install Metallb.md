# Install MetalLB in Kubernetes Cluster

```
You can access your application in the cluster in different ways:
1. by using apiserver as a proxy, but you need to pass authentication and authorization stage.
2. by using hostNetwork. When a pod is configured with hostNetwork: true, the applications running in such a pod can directly see the network interfaces of the host machine where the pod was started.
3. by using hostPort. The container port will be exposed to the external network at hostIP:hostPort, where the hostIP is the IP address of the Kubernetes node where the container is running and the hostPort is the port requested by the user.
4. by using Services with type: ClusterIP. ClusterIP Services accessible only for pods in the cluster and cluster nodes.
5. by using Services with type: NodePort. In addition to ClusterIP, this service gets random or specified by user port from range of 30000-32767. All cluster nodes listen to that port and forward all traffic to corresponding Service.
6. by using Services with type: LoadBalancer. It works only with supported Cloud Providers and with Metallb for On Premise clusters. In addition to opening NodePort, Kubernetes creates cloud load balancer that forwards traffic to NodeIP:Nodeport for that service.

So, basically: [[[ Kubernetes Service type:ClusterIP] + NodePort ] + LoadBalancer ]

7. by using Ingress (ingress-controller+Ingress object). Ingress-controller is exposed by Nodeport or LoadBalancer service and works as L7 reverse-proxy/LB for the cluster Services. It has access to ClusterIP Services so, you don't need to expose Services if you use Ingress. You can use it for SSL termination and for forwarding traffic based on URL path. The most popular ingress-controllers are:
kubernetes/nginx-ingress,
nginxinc/kubernetes-ingress,
HAProxy-ingress,
Traefik.
Now, about kubectl proxy. It uses the first way to connect to the cluster. Basically, it reads the cluster configuration in .kube/config and uses credentials from there to pass cluster API Server authentication and authorization stage. Then it creates communication channel from local machine to API-Server interface, so, you can use local port to send requests to Kubernetes cluster API without necessity to specify credentials for each request.
```


- Install Helm
	```bash
	$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
	$ chmod 700 get_helm.sh
	$ ./get_helm.sh
	```

- Install Metallb using Helm
	```bash
	$ helm repo add metallb https://metallb.github.io/metallb
	$ helm repo update
	```

- Basic Config for values.yaml
	```yaml
	configInline:
	 address-pools:
	 - name: default
	   protocol: layer2
	   addresses:
	    - 10.0.2.100-10.0.2.150
	```

- apply basic configgfile for metallb
	```bash
	$ helm install metallb metallb/metallb -f values.yaml
	```

- Verify confgmap for metallb

	```bash
	$ kubectl get configmap metallb -o yaml
	apiVersion: v1
	data:
	config: |
	address-pools: - addresses: - 10.0.2.100-10.0.2.150
	name: default
	protocol: layer2
	kind: ConfigMap
	metadata:
	annotations:
	meta.helm.sh/release-name: metallb
	meta.helm.sh/release-namespace: default
	creationTimestamp: "2022-07-05T09:33:00Z"
	labels:
	app.kubernetes.io/instance: metallb
	app.kubernetes.io/managed-by: Helm
	app.kubernetes.io/name: metallb
	app.kubernetes.io/version: v0.12.1
	helm.sh/chart: metallb-0.12.1
	name: metallb
	namespace: default
	resourceVersion: "77525"
	uid: 42a8d771-0f3c-44d4-ae19-50c4a57e4088

	```

- Use this nginx sample deployment file.
	```yml
	apiVersion: v1
	kind: Service
	metadata:
	  name: nginx
	  annotations:
	spec:
	  ports:
	  - port: 80
	    targetPort: 80
	  selector:
	    app: nginx
	  type: LoadBalancer
	---
	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: nginx-deployment
	spec:
    selector:
	    matchLabels:
	      app: nginx
	  replicas: 1
	  template:
	    metadata:
	      labels:
	        app: nginx
	  spec:
	    containers:
	      - name: nginx
	        image: nginx:1.14.2
	        ports:
	        - containerPort: 80

	```
