# Drill4J Kubernetes Configuration

1. helm install postgres (see [DEVELOPMENT.md](./DEVELOPMENT.md))
2. kubectl apply -f config-map.yml
3. kubectl apply -f admin.yml
4. kubectl apply -f apply ui.yml
5. execute metabase migration (data.sql)
	- download `data.sql` migration from releases https://github.com/Drill4J/drill-metabase-dashboards/releases
	
	```shell
		# use minikube to tunnel to PG service
		minikube service my-postgres-postgresql --url
		# adjust port value according to previous command output
		PGPASSWORD="mysecretpassword" psql -h localhost -p 63890 -U postgres -d postgres -f data.sql`
	```	
	
6. apply metabase.yml
7. open Metabase and login using default credentails (login `user@user.user`, password `useruser1`)
8. in Metabase navigate to admin settings and
	- update Databases/ Drill4J_PostgreSQL_DB host value to `my-postgres-postgresql.default.svc.cluster.local`
	- update General/ Site Url to actual value of your host (thats for UI)
9. update `DRILL_UI_BASE_URL` in config-map.yml to value to Drill UI host and restart `admin` deployment (that is required for admin to provide correct links in metrics reports)
