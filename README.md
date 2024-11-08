# Drill4J Kubernetes Configuration

1. See [DEVELOPMENT.md](./DEVELOPMENT.md) for prerequisites and development instructions

2. Install Postgres to Kubernetes cluster using Helm

	```shell
		helm install my-postgres bitnami/postgresql --set postgresqlVersion=17 \
			--set global.postgresql.auth.postgresPassword=mysecretpassword \
			--set global.postgresql.auth.database=postgres
	```

3. Create configuration values
	```shell
		kubectl apply -f ./config-map.yml
	```
4. Create admin deployment and corresponding service
	```shell
		kubectl apply -f ./admin.yml
	```

5. Create admin deployment and corresponding service
	```shell
		kubectl apply -f apply ./ui.yml
	```
6. Execute migration to create Metabase dashboards

	- download `data.sql` migration from releases https://github.com/Drill4J/drill-metabase-dashboards/releases
	
	- run

		```shell
			# use minikube to tunnel temporarily to PG service
			minikube service my-postgres-postgresql --url
			# adjust port value according to previous command output
			PGPASSWORD="mysecretpassword" psql -h localhost -p 63890 -U postgres -d postgres -f data.sql`
		```	
	
7. Create metabase and corresponding service

	```shell
		kubectl apply -f ./metabase.yml
	```

8. Open Metabase and Login using default credentails (login `user@user.user`, password `useruser1`)

	```shell
		# you can once again use minikube to temporarily tunnel to Metabase service
		minikube service metabase --url
	```

9. In Metabase UI navigate to admin settings (top right corner, gear icon, admin settings) and
	- update Databases/ Drill4J_PostgreSQL_DB host value to `my-postgres-postgresql.default.svc.cluster.local`
	- update General/ Site Url to actual value `http://host:port/path` 
	> REMEMBER to include /path configured in ingress (e.g. `/metabase`), otherwise Metabase frontend will try to fetch static resources on root path

10. Update `DRILL_UI_BASE_URL` in config-map.yml to value to Drill UI host and restart `admin` deployment (that is required for admin to provide correct links in metrics reports)

11. Enable Ingress addon

	```shell
		minikube addons enable ingress
	```

12. Configure ingress

	```shell
		kubectl apply -f ./ingress.yaml 
	```

13. Run tunnel to access ingress from host machine. Now you should be able to send requests to ingress from `http://localhost`

	```shell
		# run with elevated shell (admin privileges) 
		# keep terminal open
		minikube tunnel
	```
