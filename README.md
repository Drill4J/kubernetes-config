# Drill4J - deploy using Helm or Kubernetes

This repository describes how to deploy Drill4J core services using either Helm or Kubernetes.

## Deployment with Helm Chart

__IMPORTANT__: once you are done with this section, make sure to follow [post-deployment instructions](#post-deployment-setup-for-deployment-with-helm) to finalize setup

1. Package Helm chart

	```shell
	cd helm
	helm package .
	# ./drill-chart-0.9.0.tgz must appear in ./helm directory  
	```

2. __Option #1__ - deploy using existing secret storage
	 
	```shell
	helm install drill ./drill-chart-0.9.0.tgz \
		--set secrets.dbSecret=my-db-secrets \
		--set postgresql.auth.existingSecret=my-db-secrets
	```
	
	Secret storage ___must contain___ following keys: `"db-username"` and `"db-password"`. For example (if creating secrets with kubectl):
	```shell
	kubectl create secret generic my-db-secrets --from-literal=db-username=postgres1 --from-literal=db-password=123456
	```

	You can also override defaults key names by:
	```shell
	helm install drill ./drill-chart-0.9.0.tgz \
		--set secrets.dbSecret=my-db-secrets \
		--set secrets.dbUsernameKey=someOtherKeyForUsername \
		--set secrets.dbPasswordKey=someOtherKeyForPassword \

		--set postgresql.auth.existingSecret=my-db-secrets \
		--set postgresql.auth.secretKeys.adminPasswordKey=someOtherKeyForPassword \
		--set postgresql.auth.secretKeys.userPasswordKey=someOtherKeyForPassword
	```

3. __Option #2__ - deploy using default secrets storage. Recommended only for development purposes

	```shell
	helm install drill ./drill-chart-0.9.0.tgz --set secrets.create=true
	```
	See default values for username and password in [./helm/values.yaml](./helm/values.yaml) `secrets:` section

### Post-deployment setup for deployment with Helm

> TODO: complete

## Deployment with Kubernetes configuration files

__IMPORTANT__: once you are done with this section, make sure to follow [post-deployment instructions](#post-deployment-setup-for-deployment-with-kubernetes) to finalize setup

1. Install resources

	```shell
	# Execute commands below one by one
	# Check container logs to ensure the corresponding component is available before proceeding to the next one
	# Admin and Metabase containers are dependant on PostgreSQL and will crash otherwise
	helm install my-postgres bitnami/postgresql -f postgres.values.yaml;
	kubectl apply -f ./config-map.yaml;
	kubectl apply -f ./admin.yaml;
	kubectl apply -f ./ui.yaml;
	kubectl apply -f ./metabase.yaml;
	kubectl apply -f ./ingress.yaml;
	```

### Post-deployment setup for deployment with Kubernetes

1. Configure Metabase [following this instruction](./configure-metabase.md)

2. Update `DRILL_UI_BASE_URL` in `config-map.yaml` to point to your host.

	- change the value in `config-map.yaml` (e.g. `my.actual.host/ui`)
	- execute `kubectl apply -f config-map.yaml`
	- restart `admin` deployment (that is required for admin to provide correct links in metrics reports)

## Local Deployment with Minikube

1. See [DEVELOPMENT.md](./DEVELOPMENT.md) for prerequisites and development instructions

2. For Minikube: make sure to launch minikube first and enable Ingress addon
	
	```shell
		# Launch minikube
		minikube start
	```
	
	```shell
		# Enable Ingress
		minikube addons enable ingress
	```
	
	```shell
		# Launch dashboard to inspect state of containers and logs
		minikube dashboard
	```

	```shell
		# run with elevated shell (admin privileges) 
		# keep terminal open
		minikube tunnel
	```

3. Deploy necessary components using either Helm chart or Kubernetes config files

4. You should now have PostgreSQL, Drill4J API, Drill4J UI and Metabase Dashboards running.
	There are a few configuration steps left ahead.

	Default paths configured are:  
	- `/ui` at <http://localhost/ui> - should open Drill4J UI 
	- `/metabase` <http://localhost/metabase> - should open Metabase Dashboards
		
		> It will take a few minutes for Metabase to start and become available
		> 
		> Refer to https://drill4j.github.io/docs/getting-started/metabase for usage instructions

	- `/admin` <http://localhost/admin> - should respond with JSON `{"message":"Drill4J Admin Backend"}`

	> DB is only accessible from within cluster by default. Configure additional service if needed. 
