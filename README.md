# Ddosify Helm Charts

This [repository](https://github.com/ddosify/ddosify-helm-charts) hosts the Helm charts for the Ddosify.

There are two charts available in this repository:

- 🏠 [Ddosify Platform Self-Hosted Chart](#ddosify-platform-self-hosted-chart)
- 🐝 [Alaz Chart - Ddosify eBPF Agent](#alaz-chart---ddosify-ebpf-agent)

## What is Ddosify?

<p align="center">
<img src="assets/ddosify_service_map.png" alt="Ddosify Kubernetes Monitoring Service Map" />
<i>Ddosify automatically generates Service Map of your K8s cluster without code instrumentation or sidecars with eBPF. So you can easily find the bottlenecks in your system. Red lines indicate the high latency between services.</i>
</p>

Ddosify is an [open-source](https://github.com/ddosify/ddosify) Kubernetes Monitoring and Performance Testing platform.

## Pre-requisites

- Kubernetes 1.19+
- [Helm](https://helm.sh/docs/intro/install/) must be installed to use the Ddosify charts.

## Ddosify Platform Self-Hosted Chart

With the Ddosify Self-Hosted chart, you can deploy the Ddosify platform to your Kubernetes cluster. 

Check the [Ddosify Self-Hosted](./charts/ddosify/README.md) chart for the installation and configuration details.


## Alaz Chart - Ddosify eBPF Agent

With the Alaz chart, you can deploy the Ddosify eBPF agent to your Kubernetes cluster that you want to monitor. Alaz sends the collected data to the Ddosify platform.

Check the [Alaz](./charts/alaz/README.md) chart for the installation and configuration details.


## Communication

You can join our [Discord Server](https://discord.gg/9KdnrSUZQg) for discussions, support, and community updates.
