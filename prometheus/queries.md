# Prometheus Query Examples

These queries can be copied into the Prometheus web interface at
`http://localhost:9090` or used in Grafana panels.

## Check Monitored Targets

```promql
up
```

`1` means that a target is reachable. `0` means that Prometheus knows about the
target but cannot currently scrape it.

## Count Pods by Phase

```promql
sum by (phase) (
	kube_pod_status_phase{
		phase=~"Running|Pending|Failed"
	}
)
```

This shows how many pods are Running, Pending, or Failed.

## CPU Usage by Namespace

```promql
sum by (namespace) (
	rate(container_cpu_usage_seconds_total{
		container!="",
		container!="POD",
		namespace!=""
	}[5m])
)
```

`rate(...[5m])` calculates the average increase over the last five minutes.

## Memory Usage by Namespace

```promql
sum by (namespace) (
	container_memory_working_set_bytes{
		container!="",
		container!="POD",
		namespace!=""
	}
)
```

The result is measured in bytes. Grafana can display it as mebibytes or
gibibytes.

## Container Restarts

```promql
sum by (namespace, pod) (
	kube_pod_container_status_restarts_total{namespace!=""}
)
```

This helps identify pods that have restarted. A restart count alone does not
prove that a current incident is active; combine it with pod status and events.

## Demo Application Readiness

```promql
kube_pod_status_ready{
	namespace="observability-demo",
	condition="true"
}
```

A value of `1` means the pod is ready according to Kubernetes.

## Notes

Metric availability depends on the exporters installed in the cluster. If a
query returns no data, first test `up`, then check the metric name and labels.
