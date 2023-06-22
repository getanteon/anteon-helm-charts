# Ddosify Helm Charts

This [repository](https://github.com/ddosify/ddosify-helm-charts) hosts the Helm charts for the [Ddosify Self Hosted](https://github.com/ddosify/ddosify/tree/master/selfhosted) and is accessible via [GitHub Pages](https://ddosify.github.io/ddosify-helm-charts/).

## What is Ddosify?
Ddosify is a comprehensive performance testing platform, designed specifically to evaluate backend load and latency. It offers three distinct deployment options to cater to various needs: [Ddosify Engine](https://github.com/ddosify/ddosify/blob/master/engine_docs/README.md), [Ddosify Self-Hosted](https://github.com/ddosify/ddosify/tree/master/selfhosted), and [Ddosify Cloud](https://app.ddosify.com).


## How to Use

[Helm](https://helm.sh/docs/intro/install/) must be installed to use the Ddosify charts.

### Add and Update Repository

Once Helm has been set up correctly, add and update the repo as follows:

```bash
helm repo add ddosify https://ddosify.github.io/ddosify-helm-charts/
helm repo update
```

You can then run `helm search repo ddosify` to see the charts.

### Install Ddosify Chart

To install the ddosify chart:

```bash
kubectl create namespace ddosify
helm upgrade --install --namespace ddosify ddosify ddosify/ddosify --wait
```

### Accessing the Ddosify Dashboard (Port Forwarding)

Port forward the Ddosify service:

```bash
LOCAL_PORT=8014
kubectl port-forward --namespace ddosify service/nginx-service $LOCAL_PORT:80
```

Open the browser and navigate to `http://localhost:8014`

### Upgrading the Ddosify Chart

Make sure to [update the chart repository](#add-and-update-repository) before upgrading the chart:

```bash
helm upgrade --namespace ddosify ddosify ddosify/ddosify --wait
```

## Add New Engine (Optional):

Currently, the ddosify chart deploys a single engine. To add more engines, you can upgrade the chart with the desired number of engine count as follows. Before adding new engines, ensure that you have enabled the distributed mode by clicking the `Unlock the Distributed Mode` button in the Ddosify dashboard.

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

The recommended and tested version of PostgreSQL is `13.6` and InfluxDB is `2.6.1`. If you get any errors on different versions, please open an issue. We will try to fix it as soon as possible. And the external databases should be close to the Kubernetes cluster to avoid latency.

## Cleanup

```bash
helm delete ddosify --namespace ddosify
```

## Notes

- For AWS EKS, Amazon EKS CSI Driver addon must be enabled to use the EBS storage class. For more information, see [Amazon EKS CSI Driver](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html).
