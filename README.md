# Ddosify Helm Charts

This repository hosts the Helm charts for the [Ddosify Self Hosted](https://github.com/ddosify/ddosify/tree/master/selfhosted).

## What is Ddosify?
Ddosify is a comprehensive performance testing platform, designed specifically to evaluate backend load and latency. It offers three distinct deployment options to cater to various needs: Ddosify Engine, Ddosify Self-Hosted, and Ddosify Cloud.


## How to Use

[Helm](https://helm.sh/docs/intro/install/) must be installed to use the Ddosify charts.

- Once Helm has been set up correctly, add the repo as follows:

```bash
helm repo add ddosify https://ddosify.github.io/helm-charts/
```

- You can then run `helm search repo ddosify` to see the charts.

- To install the ddosify chart:

```bash
helm install my-ddosify ddosify/ddosify
```

- To uninstall the chart:

```bash
helm delete my-ddosify
```
