# 07 - Final Validation

## Purpose

This checklist confirms that the lab works from the Kubernetes layer to the
external observability platforms. Run it from the management server.

## 1. Check Kubernetes

```bash
kubectl get nodes -o wide
kubectl get pods -n observability-demo -o wide
kubectl get svc -n observability-demo
```

The node should be `Ready`. The healthy `demo-app` pods should be `Running`.
The controlled `crashloop-demo` pod is expected to show
`CrashLoopBackOff`; that failure is part of the exercise.

## 2. Check Prometheus and Grafana

```bash
helm list -A
kubectl get pods -n default
kubectl get prometheusrule observability-demo-alerts -n default
```

The `monitoring` release should be `deployed`, and the custom alert rule should
exist.

## 3. Check Datadog

```bash
kubectl get pods -n datadog
helm list -n datadog
```

The Datadog Agent and Cluster Agent should be `Running`. In Datadog Cloud,
confirm the cluster `cloud-native-observability-lab` shows nodes, pods, CPU,
memory, logs, and Kubernetes events.

## 4. Check Evidence

```bash
find screenshots -maxdepth 2 -type f | sort
```

Evidence should be stored in the folder that matches the tool:

- `screenshots/kubernetes/`
- `screenshots/prometheus/`
- `screenshots/grafana/`
- `screenshots/datadog/`
- `screenshots/splunk/`

## 5. Security Check

Before committing changes, search for accidental credentials:

```bash
grep -RniE 'api.?key|token|password|secret' \
  --exclude-dir=.git \
  --exclude='*.png' .
```

Replace real credentials with `REDACTED`. Never commit API keys, HEC tokens,
passwords, or kubeconfig files.

## Final Result

The lab is complete when Kubernetes is healthy, the monitoring stack is
working, Datadog receives telemetry, Splunk can search logs, the controlled
incident is documented, and the evidence files are organized.
