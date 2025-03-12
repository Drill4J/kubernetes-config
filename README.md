# Drill4J Kubernetes Configuration


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

2. Deploy necessary components.

```shell
# Execute commands below one by one
# Check container logs to ensure the corresponding component is available before proceeding to the next one
# WHY IS THIS NECESSARY: some containers are dependant on others and we don't provide Helm chart to handle that yet 
helm install my-postgres bitnami/postgresql -f postgres.values.yaml;
kubectl apply -f ./config-map.yaml;
kubectl apply -f ./admin.yaml;
kubectl apply -f ./ui.yaml;
kubectl apply -f ./metabase.yaml;
kubectl apply -f ./ingress.yaml;
```

4. You should now have PostgreSQL, Drill4J API, Drill4J UI and Metabase Dashboards running.
	There are a few configuration steps left ahead.

	Default paths configured are:  
	- `/ui` - should open Drill4J UI 
	- `/metabase` - should open Metabase Dashboards
		
		> It will take a few minutes for Metabase to start and become available
		> 
		> Refer to https://drill4j.github.io/docs/getting-started/metabase for usage instructions

	- `/admin` - should respond with JSON `{"message":"Drill4J Admin Backend"}`

	> DB is only accessible from within cluster by default. Configure additional service if needed. 

3. Configure Metabase [following this instruction](./configure-metabase.md)

4. Update `DRILL_UI_BASE_URL` in `config-map.yaml` to point to your host.
	- change the value in `config-map.yaml` (e.g. `my.actual.host/ui`)
	- execute `kubectl apply -f config-map.yaml`
	- restart `admin` deployment (that is required for admin to provide correct links in metrics reports)

## Accessing deployed resources in Minikube

When running in Minikube launch tunnel:

```shell
	# run with elevated shell (admin privileges) 
	# keep terminal open
	minikube tunnel
```

You should be able to access <http://localhost/ui>, <http://localhost/admin>, <http://localhost/metabase>