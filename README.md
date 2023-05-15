# Ddosify Helm Charts

This repository hosts the Helm charts for the [Ddosify Self Hosted](https://github.com/ddosify/ddosify/tree/master/selfhosted).

## What is Ddosify?
Ddosify is a comprehensive performance testing platform, designed specifically to evaluate backend load and latency. It offers three distinct deployment options to cater to various needs: [Ddosify Engine](https://github.com/ddosify/ddosify/blob/master/engine_docs/README.md), [Ddosify Self-Hosted](https://github.com/ddosify/ddosify/tree/master/selfhosted), and [Ddosify Cloud](https://app.ddosify.com).


## How to Use

[Helm](https://helm.sh/docs/intro/install/) must be installed to use the Ddosify charts.

- Once Helm has been set up correctly, add and update the repo as follows:

```bash
helm repo add ddosify https://ddosify.github.io/helm-charts/
helm repo update
```

- You can then run `helm search repo ddosify` to see the charts.

- To install the ddosify chart:

```bash
helm upgrade --install --namespace ddosify --create-namespace ddosify ddosify/ddosify
```

- Port forward the ddosify service:

```bash
LOCAL_PORT=8014
kubectl port-forward --namespace ddosify service/nginx-service $LOCAL_PORT:80
```

- Open the browser and navigate to `http://localhost:8014`

- To uninstall the chart:

```bash
helm delete ddosify
```
