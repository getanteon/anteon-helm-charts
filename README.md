# Anteon (Formerly Ddosify) Helm Charts

This [repository](https://github.com/getanteon/anteon-helm-charts) hosts the Helm charts for the Anteon (Formerly Ddosify).

There are two charts available in this repository:

- 🏠 [Anteon Platform Self-Hosted Chart](./charts/anteon/README.md): Deploy the Anteon Observability Platform to your Kubernetes cluster. 
- 🐝 [Alaz Chart - Anteon eBPF Agent](./charts/alaz/README.md): Deploy the Anteon eBPF agent (Alaz) to your Kubernetes cluster that you want to monitor. Alaz sends the collected data to the Anteon platform.

## What is Anteon?

<p align="center">
<img src="assets/anteon_service_map.png" alt="Anteon Kubernetes Monitoring Service Map" />
<i>Anteon automatically generates Service Map of your K8s cluster without code instrumentation or sidecars with eBPF. So you can easily find the bottlenecks in your system. Red lines indicate the high latency between services.</i>
</p>

Anteon is an [open-source](https://github.com/getanteon/anteon) Kubernetes Monitoring and Performance Testing platform.

## Pre-requisites

- Kubernetes 1.19+
- [Helm](https://helm.sh/docs/intro/install/) must be installed to use the Anteon charts.


## Communication

You can join our [Discord Server](https://discord.com/invite/9KdnrSUZQg) for discussions, support, and community updates.
