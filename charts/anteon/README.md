# Anteon Self-Hosted Chart

[Anteon Self-Hosted](https://github.com/getanteon/anteon/tree/master/selfhosted) features a web-based user interface, distributed load generation, and **Kubernetes Monitoring** capabilities. While it shares many of the same functionalities as Anteon Cloud, the Self-Hosted version is designed to be deployed within your own infrastructure for enhanced control and customization.

To install the Anteon Self-Hosted chart via Helm, run the following commands:

```bash
helm repo add anteon https://getanteon.github.io/anteon-helm-charts/
helm repo update
kubectl create namespace anteon
helm upgrade --install --namespace anteon anteon-selfhosted anteon/anteon --wait
```

## Accessing the Anteon Dashboard (Port Forwarding)

Port forward the Anteon nginx reverse proxy service to access the Anteon dashboard:

```bash
LOCAL_PORT=8014
kubectl port-forward --namespace anteon service/nginx-service $LOCAL_PORT:80
```

Open the browser and navigate to http://localhost:8014.

## Accessing the Anteon Dashboard (Ingress)

To access the Anteon dashboard via Ingress, set the `ingress.enabled` parameter to `true` in the `values.yaml` file. Set `ingress.className` to the ingress class name like `nginx` or `kong`. 

For example, to enable the Ingress and use the `kong` ingress controller, set the following values in the `values-kong-ingress.yaml` file:

```yaml
ingress:
  enabled: true
  className: "kong"
  annotations:
    cert-manager.io/cluster-issuer: acme
    konghq.com/https-redirect-status-code: '302'
    konghq.com/protocols: https
  hosts:
    - host: xxxx-xxxx.elb.eu-central-1.amazonaws.com
      paths:
        - path: /
          pathType: Prefix
  tls:
   - secretName: something-cert
     hosts:
       - xxxx-xxxx.elb.eu-central-1.amazonaws.com
```

In this example, we are using the `kong` ingress controller with the `cert-manager` for SSL termination. You must change the values according to your setup. Then, install the chart with the custom values file as follows:

```bash
helm repo add anteon https://getanteon.github.io/anteon-helm-charts/
helm repo update
kubectl create namespace anteon
helm upgrade --install --namespace anteon anteon-selfhosted anteon/anteon --values values-kong-ingress.yaml --wait
```

After the installation, you can access the Anteon dashboard via the Ingress URL. To get the Ingress URL, run the following command:

```bash
kubectl get ingress -n anteon
```

> [!TIP]
> Check [How to Securely Expose Your Kubernetes Application using NGINX Ingress](https://getanteon.com/blog/how-to-securely-expose-your-kubernetes-application-using-nginx-ingress) blog post for more information about the Ingress configuration with NGINX and Let's Encrypt.


## Upgrading the Anteon Chart

To upgrade the Anteon chart, run the following commands:

```bash
helm repo add anteon https://getanteon.github.io/anteon-helm-charts/
helm repo update
helm upgrade --install --namespace anteon anteon-selfhosted anteon/anteon --wait
```

## Anteon Chart Configuration Parameters

The following table lists the configurable parameters of the Anteon chart and their default values.

| Parameter | Description | Type | Default |
|-----------|-------------|------|---------|
| `ingress.enabled` | Enable or disable ingress. | boolean | `false` |
| `ingress.className` | Define the ingress class name like `nginx` or `kong`. | string | `""` |
| `ingress.annotations` | Annotations for the ingress, can include multiple comments for configuration. | object | `{}` |
| `ingress.hosts` | List of hosts for ingress, each with specific paths and pathTypes. | array | `[{"host": "anteon.local", "paths": [{"path": "/", "pathType": "Prefix"}]}]` |
| `ingress.tls` | TLS configuration for ingress. Uncomment and configure as needed. | array | `[]` |
| `hammer.replicas` | Number of replicas for the hammer (load generator) service. If you want to increase the engine size for performance testing, increase this value. | integer | `1` |
| `hammerdebug.replicas` | Number of replicas for the hammer debug service. | integer | `1` |
| `service.name` | The name of the outside service (nginx). You can forward the traffic with `kubectl port-forward` command to access the Anteon dashboard. | string | `"nginx-service"` |
| `service.port` | Port number the outside service (nginx) operates on. | integer | `80` |
| `service.nodePort` | Node port number for the outside service (nginx). | integer | `30000` |
| `service.replicas` | Number of replicas for the outside service (nginx). | integer | `1` |
| `influxDB.external` | Determines if InfluxDB is externally managed. | boolean | `false` |
| `influxDB.url` | URL for InfluxDB, required if `external` is `true`. | string | `"http://influxdb:8086"` |
| `influxDB.apiToken` | API token for InfluxDB, required if `external` is `true`. | string | `"5yR2qD5zCqqvjwCKKXojnPviQaB87w9JcGweVChXkhWRL"` |
| `influxDB.org` | Organization for InfluxDB, required if `external` is `true`. | string | `"anteon"` |
| `influxDB.username` | InfluxDB username, used only if `external` is `false`. | string | `"admin"` |
| `influxDB.password` | InfluxDB password, used only if `external` is `false`. | string | `"ChangeMe"` |
| `influxDB.replicas` | Number of replicas for InfluxDB. | integer | `1` |
| `postgres.external` | Determines if the Postgres server is externally managed. | boolean | `false` |
| `postgres.host` | Host for Postgres, required if `external` is `true`. | string | `"postgres"` |
| `postgres.port` | Port for Postgres, required if `external` is `true`. | integer | `5432` |
| `postgres.username` | Username for Postgres, required if `external` is `true`. | string | `"postgres"` |
| `postgres.password` | Password for Postgres, required if `external` is `true`. | string | `"ChangeMe"` |
| `postgres.replicas` | Number of replicas for Postgres. | integer | `1` |
| `pvc.influxDB.storageClassName` | Storage class name for InfluxDB persistent volume claim. | string | `""` |
| `pvc.influxDB.size` | Size of the persistent volume claim for InfluxDB. | string | `5Gi` |
| `pvc.postgres.storageClassName` | Storage class name for Postgres persistent volume claim. | string | `""` |
| `pvc.postgres.size` | Size of the persistent volume claim for Postgres. | string | `5Gi` |
| `pvc.seaweedfs.storageClassName` | Storage class name for SeaweedFS persistent volume claim. | string | `""` |
| `pvc.seaweedfs.size` | Size of the persistent volume claim for SeaweedFS. | string | `30Gi` |
| `backend.defaultMonitoringID` | Default monitoring ID (uuid) for the Kubernetes cluster. [More](#default-kubernetes-cluster-creation---monitoring-id-optional). | string | `""` |
| `backend.defaultClusterName` | Default cluster name for the Kubernetes cluster. [More](#default-kubernetes-cluster-creation---monitoring-id-optional). | string | `"default-cluster"` |
| `images.alazBackend` | Alaz Backend Docker image | string | `ddosify/selfhosted_alaz_backend:x.y.z` |
| `images.backend` | Backend Docker image | string | `ddosify/selfhosted_backend:x.y.z` |
| `images.frontend` | Frontend Docker image | string | `ddosify/selfhosted_frontend:x.y.z` |
| `images.hammer` | Hammer Docker image | string | `ddosify/selfhosted_hammer:x.y.z` |
| `images.hammermanager` | Hammer Manager Docker image | string | `ddosify/selfhosted_hammermanager:x.y.z` |
| `images.influxdb` | InfluxDB Docker image | string | `influxdb:x.y.z-alpine` |
| `images.nginx` | Nginx Docker image | string | `nginx:x.y.z-alpine` |
| `images.postgres` | Postgres Docker image | string | `postgres:x.y-alpine` |
| `images.prometheus` | Prometheus Docker image | string | `prom/prometheus:x.y.z` |
| `images.rabbitmq` | RabbitMQ Docker image | string | `rabbitmq:x.y.z-alpine` |
| `images.redis` | Redis Docker image | string | `redis:x.y.z-alpine` |
| `images.seaweedfs` | SeaweedFS Docker image | string | `chrislusf/seaweedfs:x.y` |
| `images.initContainerBusybox` | Init Container Busybox Docker image | string | `busybox:x.y.z` |
| `alazbackend.replicas` | Number of replicas for the Alaz Backend service. | integer | `3` |
| `alazbackend.celeryBeatReplicas` | Number of replicas for the Alaz Backend Celery Beat service. | integer | `1` |
| `alazbackend.celeryWorkerReplicas` | Number of replicas for the Alaz Backend Celery Worker service. | integer | `2` |
| `alazbackend.requestWriterReplicas` | Number of replicas for the Alaz Backend Request Writer service. | integer | `1` |
| `backend.replicas` | Number of replicas for the Backend service. | integer | `3` |
| `backend.celeryBeatReplicas` | Number of replicas for the Backend Celery Beat service. | integer | `1` |
| `backend.celeryWorkerReplicas` | Number of replicas for the Backend Celery Worker service. | integer | `1` |
| `frontend.replicas` | Number of replicas for the Frontend service. | integer | `1` |
| `hammermanager.replicas` | Number of replicas for the Hammer Manager service. | integer | `1` |
| `hammermanager.celeryBeatReplicas` | Number of replicas for the Hammer Manager Celery Beat service. | integer | `1` |
| `hammermanager.celeryWorkerReplicas` | Number of replicas for the Hammer Manager Celery Worker service. | integer | `1` |
| `prometheus.replicas` | Number of replicas for the Prometheus service. | integer | `1` |
| `rabbitmq.replicas` | Number of replicas for the RabbitMQ service. | integer | `1` |
| `redisBackend.replicas` | Number of replicas for the Redis Backend service. | integer | `1` |
| `redisAlazBackend.replicas` | Number of replicas for the Redis Alaz Backend service. | integer | `1` |
| `seaweedfs.replicas` | Number of replicas for the SeaweedFS service. | integer | `1` |


**Note:** The default versions of the `images.*` are placeholders. You can find the current versions of the images on the [values.yaml](./values.yaml) file.


## Add New Engine (Optional):

Currently, the Anteon chart deploys a single engine (load generator). To add more engines, you can upgrade the chart with the desired number of engine count as follows. Before adding new engines, ensure that you have enabled the distributed mode by clicking the `Unlock the Distributed Mode` button in the Anteon UI.

```bash
helm repo add anteon https://getanteon.github.io/anteon-helm-charts/
helm repo update
kubectl create namespace anteon

ENGINE_COUNT=3
helm upgrade --namespace anteon anteon-selfhosted anteon/anteon --set hammer.replicas=$ENGINE_COUNT --wait
```

## External Database (Optional):

By default, the Anteon chart deploys a PostgreSQL and InfluxDB. If you want to use them externally, you can write a custom values file (`values-external_db.yaml`) as follows:

```yaml
influxDB:
  external: true
  url: "https://us-east-1-1.aws.cloud2.influxdata.com"
  apiToken: "<your-token>"
  org: "<your-organization>"

postgres:
  external: true
  host: "postgres-rds-test.xxxxxx.rds.amazonaws.com"
  port: 5432
  username: "postgres"
  password: "<your-password>"
```

In this example, we are using the external InfluxDB Cloud and AWS RDS PostgreSQL. You must change the values according to your setup. Note that the external database must be accessible from the Kubernetes cluster. Then, you can install the chart with the custom values file as follows:

```bash
helm repo add anteon https://getanteon.github.io/anteon-helm-charts/
helm repo update
kubectl create namespace anteon
helm upgrade --install --namespace anteon anteon-selfhosted anteon/anteon --values values-external_db.yaml --wait
```

The recommended and tested version of PostgreSQL is `13.6` and InfluxDB is `2.6.1`. If you get any errors on different versions, please open an issue. We will try to fix it as soon as possible. To avoid the latency, the external databases should be as close as possible to the Kubernetes cluster.

## Storage Class (Optional):

If you want to use a specific storage class for the persistent volume claims, you can set the storage class name. To list all the storage classes in the cluster, run the following command:

```bash
kubectl get storageclass
```

There are three persistent volume claims in the Anteon chart: `InfluxDB`, `Postgres`, and `SeaweedFS`. In this example, we are using the `nfs-client` storage class. 

You can set the storage class name for each PVC in two ways.

### (Option 1) Set the Storage Class Name in the Helm Command

You can set the storage class name in the Helm command as follows:

```bash
helm repo add anteon https://getanteon.github.io/anteon-helm-charts/
helm repo update
kubectl create namespace anteon
helm upgrade --install --namespace anteon anteon-selfhosted anteon/anteon \
  --set pvc.influxDB.storageClassName="nfs-client" \
  --set pvc.postgres.storageClassName="nfs-client" \
  --set pvc.seaweedfs.storageClassName="nfs-client" --wait
```

### (Option 2) Write a Custom Values File

You can write a custom values file (`values-storageclass.yaml`) as follows:

```yaml
pvc:
  influxDB:
    storageClassName: "nfs-client"
  postgres:
    storageClassName: "nfs-client"
  seaweedfs:
    storageClassName: "nfs-client"
```

Then, you can install the chart with the custom values file as follows:

```bash
helm repo add anteon https://getanteon.github.io/anteon-helm-charts/
helm repo update
kubectl create namespace anteon
helm upgrade --install --namespace anteon anteon-selfhosted anteon/anteon --values values-storageclass.yaml --wait
```

## Default Kubernetes Cluster Creation - Monitoring ID (Optional):

If you want to create a default Kubernetes cluster in the Anteon UI, you can set the `backend.defaultMonitoringID` and `backend.defaultClusterName` parameters in the Helm command.

```bash
helm repo add anteon https://getanteon.github.io/anteon-helm-charts/
helm repo update
kubectl create namespace anteon
helm upgrade --install --namespace anteon anteon-selfhosted anteon/anteon --set backend.defaultMonitoringID=53f8601a-37cb-4f85-abd3-9b563217e593 --set backend.defaultClusterName="default-cluster" --wait
```

In this example, we are creating a default Kubernetes cluster with the monitoring ID `53f8601a-37cb-4f85-abd3-9b563217e593` and the cluster name `default-cluster`. You must change the values according to your setup. You should set a uuid for the `backend.defaultMonitoringID` parameter. This creates only one default cluster. If you want to create more clusters, you can do it from the Anteon UI.

## Cleanup

```bash
helm delete anteon-selfhosted --namespace anteon
```

## Telemetry Data

Anteon collects telemetry data to improve the product. You can disable it by setting the `ANONYMOUS_TELEMETRY_ENABLED` environment variable to `False` in the [backend](./charts/anteon/templates/backend.yaml) deployment.

```yaml
...
env:
  - name: ANONYMOUS_TELEMETRY_ENABLED
    value: "False"
...
```

Check the example telemetry data that Anteon collects from [here](https://github.com/getanteon/anteon/tree/master/selfhosted#example-data).

# Notes

- For AWS EKS, Amazon EKS CSI Driver addon must be enabled to use the EBS storage class. For more information, see [Amazon EKS CSI Driver](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html).
