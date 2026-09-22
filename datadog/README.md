# Datadog Integration

## Purpose

The Datadog Agent sends Kubernetes metrics, events, and container logs from the
local cluster to Datadog Cloud.

## Current Status

Datadog is installed with the official Helm chart:

```bash
helm list -n datadog
kubectl get pods -n datadog
```

The validated workloads are:

- Datadog Node Agent: `3/3 Running`;
- Datadog Cluster Agent: `1/1 Running`;
- Datadog Operator: `1/1 Running`.

The cluster is named `cloud-native-observability-lab` in Datadog Cloud.

## Security

The API key is stored in the Kubernetes Secret `datadog-secret`. It must not be
written in this repository, screenshots, or command history.

## Cloud Validation

Open [Datadog Cloud](https://app.datadoghq.com) and check the Kubernetes
cluster overview. It should show nodes, pods, CPU, memory, logs, and events.

Evidence is stored in `screenshots/datadog/`.
