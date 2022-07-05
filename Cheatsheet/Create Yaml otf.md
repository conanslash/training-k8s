- Create a deployment yaml without actually creating the deployment

	`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`

- Create a deployment yaml and redirect it to a file

	`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml`

- You can directly edit the deployment yaml as follows:

	`kubectl edit deployment/nginx`