
## Prerequisites

1. Install [minikube](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)
2. Install kubectl

    ```shell
        minikube kubectl -- get po -A
    ```
3. Install [Helm](https://helm.sh/docs/intro/quickstart/#install-helm)
    

## Running

1. Run minikube

    ```shell
        minikube start
    ```

2. Run dashboard web UI

    ```shell
        minikube dashboard
    ```

## Configuring new resources in Kubernetes

```
kubectl apply -f config.yaml
```

In this case config refers to any sort of resource definition within Kubernetes - deployment, service, statefulset, volume, volume claim, ingress. See Kubernetes and kubectl docs for more info.

## Install PostgreSQL to cluster

```shell
    helm install my-postgres bitnami/postgresql --set postgresqlVersion=17 \
    --set global.postgresql.auth.postgresPassword=mysecretpassword \
    --set global.postgresql.auth.database=postgres
```

## Getting host name

Host name format is `<service>.<namespace>.svc.cluster.local`

1. Get services

    ```shell
        kubectl get svc
    ```

2. Get namespace
    
    ```shell
        kubectl get svc my-postgres-postgresql -o jsonpath='{.metadata.namespace}'
    ```

## Call service from host machine

Minikube runs with docker-in-docker. Because of that additional actions are required to call services from host machine.

```shell
    kubectl get svc
    minikube service <service-name> --url
```

This will create tunnel from host machine to the specified service. In this case [Service](https://kubernetes.io/docs/concepts/services-networking/service/) is a Kubernetes term, so make sure that resources Service points to (Deployment or StatefulSets) are actually running