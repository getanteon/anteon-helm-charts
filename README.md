# Ddosify Helm Charts

This [repository](https://github.com/ddosify/ddosify-helm-charts) hosts the Helm charts for the Ddosify.

There are two charts available in this repository:

- 🏠 [Ddosify Self-Hosted Chart](#ddosify-self-hosted-chart)
- 🐝 [Alaz Chart - Ddosify eBPF Agent](#alaz-chart---ddosify-ebpf-agent)

## What is Ddosify?

<p align="center">
<img src="https://imagedelivery.net/jnIqn6NB1gbMLXIvlYKo5A/5ed79d96-aef4-467d-f5d0-e17cc5c3e700/public" alt="Ddosify - Self-Hosted" />
</p>

Ddosify is a comprehensive performance testing and observability platform. It consists of the following components:

- ⚡️ [Ddosify Engine](https://github.com/ddosify/ddosify/blob/master/engine_docs/README.md): High-performance, single engine load testing tool.
- 🏠 [Ddosify Self-Hosted](https://github.com/ddosify/ddosify/tree/master/selfhosted): Self-hosted, distributed load testing and observability platform.
- ☁️ [Ddosify Cloud](https://app.ddosify.com): Cloud-based, distributed load testing and observability platform.
- 🐝 [Alaz](https://github.com/ddosify/alaz): Ddosify eBPF agent for Kubernetes. It can inspect and collect Kubernetes (K8s) service traffic without the need for code instrumentation, sidecars, or service restarts. It also gets system information and resources (CPU, Memory, Disk and Network) via the Prometheus Node Exporter.

## Pre-requisites

- Kubernetes 1.19+
- [Helm](https://helm.sh/docs/intro/install/) must be installed to use the Ddosify charts.

## Ddosify Self-Hosted Chart

[Ddosify Self-Hosted](https://github.com/ddosify/ddosify/tree/master/selfhosted) features a web-based user interface, distributed load generation, and **Kubernetes Monitoring** capabilities. While it shares many of the same functionalities as Ddosify Cloud, the Self-Hosted version is designed to be deployed within your own infrastructure for enhanced control and customization.

To install the Ddosify Self-Hosted chart via Helm, run the following commands:

```bash
helm repo add ddosify https://ddosify.github.io/ddosify-helm-charts/
helm repo update
kubectl create namespace ddosify
helm upgrade --install --namespace ddosify ddosify ddosify/ddosify --wait
```

### Accessing the Ddosify Dashboard (Port Forwarding)

Port forward the Ddosify nginx reverse proxy service to access the Ddosify dashboard:

```bash
LOCAL_PORT=8014
kubectl port-forward --namespace ddosify service/nginx-service $LOCAL_PORT:80
```

Open the browser and navigate to http://localhost:8014.

### Upgrading the Ddosify Chart

To upgrade the Ddosify chart, run the following commands:

```bash
helm repo add ddosify https://ddosify.github.io/ddosify-helm-charts/
helm repo update
helm upgrade --namespace ddosify ddosify ddosify/ddosify --wait
```

### Add New Engine (Optional):

Currently, the Ddosify chart deploys a single engine (load generator). To add more engines, you can upgrade the chart with the desired number of engine count as follows. Before adding new engines, ensure that you have enabled the distributed mode by clicking the `Unlock the Distributed Mode` button in the Ddosify UI.

```bash
ENGINE_COUNT=3
helm upgrade --namespace ddosify ddosify ddosify/ddosify --set hammerReplicas=$ENGINE_COUNT --wait
```

### External Database (Optional):

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

### Cleanup

```bash
helm delete ddosify --namespace ddosify
```

### Telemetry Data

Ddosify collects telemetry data to improve the product. You can disable it by setting the `ANONYMOUS_TELEMETRY_ENABLED` environment variable to `False` in the [backend](./charts/ddosify/templates/backend.yaml) deployment.

```yaml
...
env:
  - name: ANONYMOUS_TELEMETRY_ENABLED
    value: "False"
...
```

Check the example telemetry data that Ddosify collects from [here](https://github.com/ddosify/ddosify/tree/master/selfhosted#example-data).

## Alaz Chart - Ddosify eBPF Agent

[Alaz](https://github.com/ddosify/alaz) is an open-source Ddosify eBPF agent that can inspect and collect Kubernetes (K8s) service traffic without the need for code instrumentation, sidecars, or service restarts. This is possible due to its use of eBPF technology. Alaz can create a Service Map that helps identify golden signals and problems like high latencies, 5xx errors, zombie services, SQL queries. Additionally, it can gather system information and resources via the Prometheus Node Exporter, which is readily available on the agent.

To install the Alaz chart, first, you need to add the Ddosify Helm repository:

```bash
helm repo add ddosify https://ddosify.github.io/ddosify-helm-charts/
helm repo update
```

Then, you can install the Alaz chart with the following command:
```bash
# Replace <MONITORING_ID> with your monitoring ID from the Ddosify UI. Change XXXXX with your monitoring ID.
MONITORING_ID=XXXXX
# Set BACKEND_HOST with your Ddosify Self-Hosted Endpoint. If your Ddosify Self-Hosted endpoint is http://localhost:8014, then BACKEND_HOST=http://localhost:8014/api
BACKEND_HOST=XXXXX
helm repo add ddosify https://ddosify.github.io/ddosify-helm-charts/
helm repo update
kubectl create namespace ddosify
helm upgrade --install --namespace ddosify alaz ddosify/alaz --set monitoringID=$MONITORING_ID --set backendHost=$BACKEND_HOST
```

For more installation methods, see [Alaz (Ddosify eBPF Agent)](https://github.com/ddosify/alaz).

### Alaz Parameters

The following table lists the configurable parameters of the Alaz chart and their default values.

| Parameter | Description | Type | Default |
| --- | --- | --- | --- |
| `monitoringID` | **CHANGE**: Monitoring ID (required). You can get it from Ddosify UI, Cluster Detail Page. | string | `<monitoringID>` |
| `backendHost` | Backend host URL to send the collected data. Default is [Ddosify Cloud](https://app.ddosify.com/). If you have Ddosify Self-Hosted installed, you can give the backend URL. | string | `https://api.ddosify.com:443` |
| `namespace` | Namespace to deploy the chart | string | `ddosify` |
| `logLevel` | [Zero log](https://github.com/rs/zerolog) level (0: trace, 1: debug, 2: info, 3: warn, 4: error, 5: fatal, 6: panic) | int | `1` |
| `resources.limits.cpu` | CPU limit | string | `1` |
| `resources.limits.memory` | Memory limit | string | `1Gi` |
| `resources.requests.cpu` | CPU request | string | `500m` |
| `resources.requests.memory` | Memory request | string | `400Mi` |
| `image` | Alaz Docker image | string | `ddosify/alaz:v0.1.4` |
| `imagePullPolicy` | Image pull policy | string | `IfNotPresent` |
| `containerPort` | Container port for debugging and profiling Alaz | int | `8181` |
| `podAnnotations` | Annotations to add to the pod | object | `{}` |
| `metricsEnabled` | Enable prometheus node exporter metrics (cpu, memory, network, disk, etc.) | bool | `true` |
| `serviceMapEnabled` | Enable service map for K8s network traffic using eBPF | bool | `true` |
| `distTracingEnabled` | Enable distributed tracing using eBPF | bool | `true` |

You can override these default values by creating a `values.yaml` file and specifying your own values or using the `--set` flag during installation.

## Notes

- For AWS EKS, Amazon EKS CSI Driver addon must be enabled to use the EBS storage class. For more information, see [Amazon EKS CSI Driver](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html).
