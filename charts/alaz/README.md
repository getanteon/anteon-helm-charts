# Alaz Chart - Ddosify eBPF Agent

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

## Alaz Chart Configuration Parameters

The following table lists the configurable parameters of the Alaz chart and their default values.

| Parameter | Description | Type | Default |
| --- | --- | --- | --- |
| `monitoringID` | **CHANGE**: Monitoring ID (required). You can get it from Ddosify UI, Cluster Detail Page. | string | `""` |
| `backendHost` | Backend host URL to send the collected data. Default is [Ddosify Cloud](https://app.ddosify.com/). If you have Ddosify Self-Hosted installed, you can give the backend URL. | string | `https://api.ddosify.com:443` |
| `namespace` | Namespace to deploy the chart | string | `ddosify` |
| `logLevel` | [Zero log](https://github.com/rs/zerolog) level (0: trace, 1: debug, 2: info, 3: warn, 4: error, 5: fatal, 6: panic) | int | `1` |
| `resources.limits.cpu` | CPU limit | string | `1` |
| `resources.limits.memory` | Memory limit | string | `1Gi` |
| `resources.requests.cpu` | CPU request | string | `500m` |
| `resources.requests.memory` | Memory request | string | `400Mi` |
| `image` | Alaz Docker image | string | - |
| `imagePullPolicy` | Image pull policy | string | `IfNotPresent` |
| `containerPort` | Container port for debugging and profiling Alaz | int | `8181` |
| `podAnnotations` | Annotations to add to the pod | object | `{}` |
| `metricsEnabled` | Enable prometheus node exporter metrics (cpu, memory, network, disk, etc.) | bool | `true` |
| `serviceMapEnabled` | Enable service map for K8s network traffic using eBPF | bool | `true` |
| `distTracingEnabled` | Enable distributed tracing using eBPF | bool | `true` |

You can override these default values by creating a `values.yaml` file and specifying your own values or using the `--set` flag during installation.
