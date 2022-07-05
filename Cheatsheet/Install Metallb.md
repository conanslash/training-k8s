# Install MetalLB in Kubernetst Cluster

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
