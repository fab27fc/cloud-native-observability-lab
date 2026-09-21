# 04 - Alerting

## Objective

Create Kubernetes alert rules for repeated pod restarts and demo application
unavailability using the Prometheus Operator.

## Alert Rules

The rules are defined in
`prometheus/alert-rules.yaml` as a `PrometheusRule` resource in the `default`
namespace. The resource is labeled with `release: monitoring` so the
`kube-prometheus-stack` Prometheus instance selects it.

### KubernetesPodCrashLooping

This alert fires when a pod in `observability-demo` restarts more than twice
within ten minutes and the condition remains true for five minutes.

```promql
increase(kube_pod_container_status_restarts_total{
	namespace="observability-demo"
}[10m]) > 2
```

Severity: `warning`

### ApplicationUnavailable

This alert fires when the available replicas for `demo-app` are lower than the
requested replicas for five minutes.

```promql
kube_deployment_status_replicas_available{
	namespace="observability-demo",
	deployment="demo-app"
}
<
kube_deployment_spec_replicas{
	namespace="observability-demo",
	deployment="demo-app"
}
```

Severity: `critical`

## Validation

Validate the custom rule resource:

```bash
kubectl get prometheusrule observability-demo-alerts -n default
```

Confirm the alert names are loaded by Prometheus through its rules API or the
Prometheus web interface:

```bash
curl -sS http://127.0.0.1:9090/api/v1/rules
```

The controlled `CrashLoopBackOff` scenario will be created later to produce
real firing evidence for `KubernetesPodCrashLooping`.
