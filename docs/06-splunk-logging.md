# 06 - Splunk Logging

## What This Integration Does

Splunk is used to search application and Kubernetes logs. The data flow is
is:

```text
Container logs -> Splunk OpenTelemetry Collector -> Splunk HEC -> Splunk search
```

The Collector runs inside Kubernetes and forwards logs to Splunk Cloud through
the HTTP Event Collector (HEC).

## Current Status

This integration has been validated. The Splunk OpenTelemetry Collector is
running in the `splunk-otel` namespace and Splunk Cloud contains searchable
Kubernetes log events.

Verify the status with:

```bash
kubectl get pods -A | grep -Ei "splunk|otel"
helm list -A | grep -Ei "splunk|otel"
```

An empty result means that the integration is not available in the current
cluster.

## Installation Requirements

Before installing the Collector, obtain:

1. A Splunk Cloud HEC endpoint.
2. A HEC token with permission to ingest events.
3. The correct Splunk Cloud index.
4. A Kubernetes Secret for the HEC token.

Never put the HEC token directly in a YAML file committed to Git.

## Validation

After installation, validate the Collector:

```bash
kubectl get pods -n splunk-otel
kubectl logs -n splunk-otel -l app.kubernetes.io/name=splunk-otel-collector
```

Then create or observe a log from the `observability-demo` namespace and search
Splunk by namespace, pod, or container. The searches returned real events and
the evidence was saved after validation.

Expected evidence files:

- `screenshots/splunk/16-splunk-collector-daemonset.png`
- `screenshots/splunk/17-splunk-hec-logs.png`
- `screenshots/splunk/18-splunk-search-by-pod.png`
- `screenshots/splunk/19-splunk-search-by-namespace.png`
