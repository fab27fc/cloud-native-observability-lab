# RCA-001 - Grafana No Data

## Summary

Grafana can display `No data` when it cannot obtain a time series from
Prometheus. This is usually a data path or query problem, not a Grafana chart
problem.

## Detection

The symptom is an empty panel or the message `No data` in the Grafana
dashboard.

## Investigation

Check the monitoring pods and Prometheus service:

```bash
kubectl get pods -n default
kubectl get svc -n default | grep prometheus
```

Test Prometheus directly through a temporary port-forward:

```bash
kubectl port-forward -n default \
	svc/monitoring-kube-prometheus-prometheus 9090:9090
curl -sS 'http://127.0.0.1:9090/api/v1/query?query=up'
```

If the API returns `"status":"success"` and data, Prometheus is working. The
next checks are Grafana's data source, dashboard time range, and panel query.

## Root Cause Categories

- Prometheus or Grafana is not running.
- The Grafana data source points to the wrong service.
- The dashboard time range has no recent samples.
- The PromQL metric or label filter does not exist in this environment.
- An exporter is not exposing the expected metric.

## Resolution

Follow [runbooks/grafana-no-data.md](../runbooks/grafana-no-data.md):

1. Confirm Prometheus and Grafana are Running.
2. Confirm the Prometheus service exists.
3. Test the simple `up` query.
4. Use Grafana **Save & test** for the Prometheus data source.
5. Select a recent time range such as **Last 15 minutes**.
6. Reintroduce the original panel query after `up` works.

## Prevention

Test new PromQL queries in Prometheus before adding them to Grafana. Record
environment-specific metric limitations instead of documenting a panel as
working when it returns no data.
