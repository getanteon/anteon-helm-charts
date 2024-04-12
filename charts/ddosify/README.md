# Ddosify Self-Hosted Chart

[Ddosify Self-Hosted](https://github.com/ddosify/ddosify/tree/master/selfhosted) features a web-based user interface, distributed load generation, and **Kubernetes Monitoring** capabilities. While it shares many of the same functionalities as Ddosify Cloud, the Self-Hosted version is designed to be deployed within your own infrastructure for enhanced control and customization.

To install the Ddosify Self-Hosted chart via Helm, run the following commands:

```bash
helm repo add ddosify https://ddosify.github.io/ddosify-helm-charts/
helm repo update
kubectl create namespace ddosify
helm upgrade --install --namespace ddosify ddosify ddosify/ddosify --wait
```

## Accessing the Ddosify Dashboard (Port Forwarding)

Port forward the Ddosify nginx reverse proxy service to access the Ddosify dashboard:

```bash
LOCAL_PORT=8014
kubectl port-forward --namespace ddosify service/nginx-service $LOCAL_PORT:80
```

Open the browser and navigate to http://localhost:8014.

## Upgrading the Ddosify Chart

To upgrade the Ddosify chart, run the following commands:

```bash
helm repo add ddosify https://ddosify.github.io/ddosify-helm-charts/
helm repo update
helm upgrade --namespace ddosify ddosify ddosify/ddosify --wait
```

## Ddosify Chart Configuration Parameters

The following table lists the configurable parameters of the Ddosify chart and their default values.

| Parameter | Description | Type | Default |
|-----------|-------------|------|---------|
| `ingress.enabled` | Enable or disable ingress. | boolean | `false` |
| `ingress.className` | Define the ingress class name like `nginx` or `kong`. | string | `""` |
| `ingress.annotations` | Annotations for the ingress, can include multiple comments for configuration. | object | `{}` |
| `ingress.hosts` | List of hosts for ingress, each with specific paths and pathTypes. | array | `[{"host": "ddosify.local", "paths": [{"path": "/", "pathType": "Prefix"}]}]` |
| `ingress.tls` | TLS configuration for ingress. Uncomment and configure as needed. | array | `[]` |
| `hammerReplicas` | Number of replicas for the hammer (load generator) service. If you want to increase the engine size for performance testing, increase this value. | integer | `1` |
| `service.name` | The name of the outside service (nginx). You can forward the traffic with `kubectl port-forward` command to access the Ddosify dashboard. | string | `"nginx-service"` |
| `service.port` | Port number the outside service (nginx) operates on. | integer | `80` |
| `service.nodePort` | Node port number for the outside service (nginx). | integer | `30000` |
| `influxDB.external` | Determines if InfluxDB is externally managed. | boolean | `false` |
| `influxDB.url` | URL for InfluxDB, required if `external` is `true`. | string | `"http://influxdb:8086"` |
| `influxDB.apiToken` | API token for InfluxDB, required if `external` is `true`. | string | `"5yR2qD5zCqqvjwCKKXojnPviQaB87w9JcGweVChXkhWRL"` |
| `influxDB.org` | Organization for InfluxDB, required if `external` is `true`. | string | `"ddosify"` |
| `influxDB.username` | InfluxDB username, used only if `external` is `false`. | string | `"admin"` |
| `influxDB.password` | InfluxDB password, used only if `external` is `false`. | string | `"ChangeMe"` |
| `postgres.external` | Determines if the Postgres server is externally managed. | boolean | `false` |
| `postgres.host` | Host for Postgres, required if `external` is `true`. | string | `"postgres"` |
| `postgres.port` | Port for Postgres, required if `external` is `true`. | integer | `5432` |
| `postgres.username` | Username for Postgres, required if `external` is `true`. | string | `"postgres"` |
| `postgres.password` | Password for Postgres, required if `external` is `true`. | string | `"ChangeMe"` |
| `pvc.influxDB.storageClassName` | Storage class name for InfluxDB persistent volume claim. | string | `""` |
| `pvc.influxDB.size` | Size of the persistent volume claim for InfluxDB. | string | `5Gi` |
| `pvc.postgres.storageClassName` | Storage class name for Postgres persistent volume claim. | string | `""` |
| `pvc.postgres.size` | Size of the persistent volume claim for Postgres. | string | `5Gi` |
| `pvc.seaweedfs.storageClassName` | Storage class name for SeaweedFS persistent volume claim. | string | `""` |
| `pvc.seaweedfs.size` | Size of the persistent volume claim for SeaweedFS. | string | `30Gi` |
| `images.alazBackend` | Alaz Backend Docker image | string | `ddosify/selfhosted_alaz_backend:x.y.z` |
| `images.backend` | Backend Docker image | string | `ddosify/selfhosted_backend:x.y.z` |
| `images.frontend` | Frontend Docker image | string | `ddosify/selfhosted_frontend:x.y.z` |
| `images.hammer` | Hammer Docker image | string | `ddosify/selfhosted_hammer:x.y.z` |
| `images.hammermanager` | Hammer Manager Docker image | string | `ddosify/selfhosted_hammermanager:x.y.z` |
| `images.influxdb` | InfluxDB Docker image | string | `influxdb:x.y.z` |
| `images.nginx` | Nginx Docker image | string | `nginx:x.y.z` |
| `images.postgres` | Postgres Docker image | string | `postgres:x.y.z` |
| `images.prometheus` | Prometheus Docker image | string | `prom/prometheus:x.y.z` |
| `images.rabbitmq` | RabbitMQ Docker image | string | `rabbitmq:x.y.z` |
| `images.redis` | Redis Docker image | string | `redis:x.y.z` |
| `images.seaweedfs` | SeaweedFS Docker image | string | `chrislusf/seaweedfs:x.y.z` |
| `images.initContainerBusybox` | Init Container Busybox Docker image | string | `busybox:x.y.z` |

**Note:** The default versions of the `images.*` are placeholders. You can find the current versions of the images on the [values.yaml](./values.yaml) file.


## Add New Engine (Optional):

Currently, the Ddosify chart deploys a single engine (load generator). To add more engines, you can upgrade the chart with the desired number of engine count as follows. Before adding new engines, ensure that you have enabled the distributed mode by clicking the `Unlock the Distributed Mode` button in the Ddosify UI.

```bash
ENGINE_COUNT=3
helm upgrade --namespace ddosify ddosify ddosify/ddosify --set hammerReplicas=$ENGINE_COUNT --wait
```

## External Database (Optional):

By default, the Ddosify chart deploys a PostgreSQL and InfluxDB. If you want to use them externally, you can write a custom values file (`values-external_db.yaml`) as follows:

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
helm upgrade --install --namespace ddosify ddosify ddosify/ddosify --values values-external_db.yaml --wait
```

The recommended and tested version of PostgreSQL is `13.6` and InfluxDB is `2.6.1`. If you get any errors on different versions, please open an issue. We will try to fix it as soon as possible. To avoid the latency, the external databases should be as close as possible to the Kubernetes cluster.

## Cleanup

```bash
helm delete ddosify --namespace ddosify
```

## Telemetry Data

Ddosify collects telemetry data to improve the product. You can disable it by setting the `ANONYMOUS_TELEMETRY_ENABLED` environment variable to `False` in the [backend](./charts/ddosify/templates/backend.yaml) deployment.

```yaml
...
env:
  - name: ANONYMOUS_TELEMETRY_ENABLED
    value: "False"
...
```

Check the example telemetry data that Ddosify collects from [here](https://github.com/ddosify/ddosify/tree/master/selfhosted#example-data).

# Notes

- For AWS EKS, Amazon EKS CSI Driver addon must be enabled to use the EBS storage class. For more information, see [Amazon EKS CSI Driver](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html).
