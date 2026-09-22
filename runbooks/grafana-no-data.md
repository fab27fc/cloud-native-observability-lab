# Runbook - Grafana Shows No Data

## Purpose

Use this guide when a Grafana panel displays `No data` or an empty graph.
Grafana does not collect metrics itself. It asks Prometheus for the data, so
the investigation should check the connection from Grafana to Prometheus.

## Symptoms

- A Grafana panel displays `No data`.
- A dashboard loads, but the charts are empty.
- A PromQL query works in one place but not in the dashboard.

## Investigation Steps

### 1. Check the monitoring pods

```bash
kubectl get pods -n default
```

Prometheus and Grafana should show `Running`.

### 2. Check the Prometheus service

```bash
kubectl get svc -n default | grep prometheus
```

The service `monitoring-kube-prometheus-prometheus` should exist.

### 3. Test Prometheus directly

Create a temporary connection:

```bash
kubectl port-forward \
	-n default \
	svc/monitoring-kube-prometheus-prometheus \
	9090:9090
```

In another terminal, test the Prometheus API:

```bash
curl -sS http://127.0.0.1:9090/api/v1/query?query=up
```

Look for a response containing `"status":"success"`. A result value of `1`
normally means that a monitored target is reachable.

### 4. Test the same query in Prometheus

Open `http://localhost:9090` and run:

```promql
up
```

If this query returns data, Prometheus is collecting metrics. The problem is
probably the Grafana data source, dashboard time range, or panel query.

### 5. Check the Grafana data source

Open Grafana at `http://localhost:3000` and go to:

```text
Connections -> Data sources -> Prometheus
```

Use **Save & test**. The data source should report that the connection works.
Do not place passwords or tokens in the repository or in screenshots.

### 6. Check the dashboard time range

Set the dashboard time range to a recent value such as **Last 15 minutes**.
Metrics outside the selected time range will not appear in the panel.

### 7. Check the panel query

Try a simple query first:

```promql
up
```

If `up` works but the original query does not, inspect its metric name,
namespace filter, labels, and time range. A label filter that does not match
the current cluster returns no series.

## Common Causes

| Cause | What to check |
|---|---|
| Prometheus is not running | `kubectl get pods -n default` |
| Wrong service name | `kubectl get svc -n default` |
| Grafana data source is incorrect | Use **Save & test** |
| Time range is too old | Select **Last 15 minutes** |
| Query labels do not match | Test with `up`, then add filters |
| Metric is not available | Check the Prometheus query result |

## Recovery

1. Restore the Prometheus pod or service if it is missing.
2. Correct the Grafana Prometheus data source.
3. Set a recent dashboard time range.
4. Test with `up` before restoring a more complex query.
5. Re-run the original panel query.

## Related Files

- [docs/02-prometheus-grafana.md](../docs/02-prometheus-grafana.md)
- [docs/03-operational-dashboard.md](../docs/03-operational-dashboard.md)
- [runbooks/grafana-no-data.md](grafana-no-data.md)
