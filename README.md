# Drill4J Kubernetes Configuration

1. helm install postgres
2. apply config-map.yml
3. apply admin.yml
4. apply ui.yml
5. execute metabase migration (data.sql)
6. apply metabase.yml
7. open metabase, navigate to admin settings and
	- update Databases/ Drill4J_PostgreSQL_DB host value to my-postgres-postgresql.default.svc.cluster.local
	- update General/ Site Url to actual value of host (thats for UI)
8. update value to Drill UI host in config-map.yml and restart admin
