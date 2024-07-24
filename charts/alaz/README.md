# Alaz Chart - Anteon eBPF Agent

[Alaz](https://github.com//alaz) is an open-source Anteon eBPF agent that can inspect and collect Kubernetes (K8s) service traffic without the need for code instrumentation, sidecars, or service restarts. This is possible due to its use of eBPF technology. Alaz can create a Service Map that helps identify golden signals and problems like high latencies, 5xx errors, zombie services, SQL queries. Additionally, it can gather system information and resources via the Prometheus Node Exporter, which is readily available on the agent.

To install the Alaz chart, first, you need to add the Anteon Helm repository:

```bash
helm repo add anteon https://getanteon.github.io/anteon-helm-charts/
helm repo update
```

Then, you can install the Alaz chart with the following command:
```bash
# Replace <MONITORING_ID> with your monitoring ID from the Anteon UI. Change XXXXX with your monitoring ID.
MONITORING_ID=XXXXX
# Set BACKEND_HOST with your Anteon Self-Hosted Endpoint. If your Anteon Self-Hosted endpoint is http://localhost:8014, then BACKEND_HOST=http://localhost:8014/api
BACKEND_HOST=XXXXX
helm repo add anteon https://getanteon.github.io/anteon-helm-charts/
helm repo update
kubectl create namespace anteon
helm upgrade --install --namespace anteon alaz anteon/alaz --set monitoringID=$MONITORING_ID --set backendHost=$BACKEND_HOST
```

For more installation methods, see [Alaz (Anteon eBPF Agent)](https://github.com/getanteon/alaz).

## Alaz Chart Configuration Parameters

The following table lists the configurable parameters of the Alaz chart and their default values.

| Parameter | Description | Type | Default |
| --- | --- | --- | --- |
| `monitoringID` | **CHANGE**: Monitoring ID (required). You can get it from Anteon UI, Cluster Detail Page. | string | `""` |
| `backendHost` | Backend host URL to send the collected data. Default is [Anteon Cloud](https://app.getanteon.com/). If you have Anteon Self-Hosted installed, you can give the backend URL. | string | `https://api-alaz.getanteon.com:443` |
| `namespace` | Namespace to deploy the chart | string | `anteon` |
| `logLevel` | [Zero log](https://github.com/rs/zerolog) level (0: trace, 1: debug, 2: info, 3: warn, 4: error, 5: fatal, 6: panic) | int | `1` |
| `resources.limits.cpu` | CPU limit | string | `1` |
| `resources.limits.memory` | Memory limit | string | `1Gi` |
| `resources.requests.memory` | Memory request | string | `400Mi` |
| `image` | Alaz Docker image | string | - |
| `imagePullPolicy` | Image pull policy | string | `IfNotPresent` |
| `containerPort` | Container port for debugging and profiling Alaz | int | `8181` |
| `podAnnotations` | Annotations to add to the pod | object | `{}` |
| `metricsEnabled` | Enable prometheus node exporter metrics (cpu, memory, network, disk, etc.) | bool | `true` |
| `tracingEnabled` | Enable tracing (service map and distributed tracing) using eBPF | bool | `true` |
| `logsEnabled` | Enable logging | bool | `false` |
| `excludedNamespaces` | Namespaces to exclude from monitoring with regex format. For example: `"^anteon.*"` to exclude all namespaces starting with "anteon" | string | `""` |
| `criRuntimeEndpoint` | Custom CRI runtime endpoint (optional) | string | `""` |

You can override these default values by creating a `values.yaml` file and specifying your own values or using the `--set` flag during installation.
