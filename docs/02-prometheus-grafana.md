# 02 - Prometheus and Grafana

## What This Step Does

Prometheus collects numbers about the cluster. These numbers are called
metrics. Grafana reads the metrics from Prometheus and displays them in charts.

The flow is:

```text
Kubernetes -> exporters -> Prometheus -> Grafana dashboard
```

Examples of metrics are:

- how many pods are running;
- how much CPU a namespace uses;
- how much memory a container uses;
- how many times a container restarted.

## Check the Monitoring Components

Run these commands from the management server:

```bash
helm list -A
kubectl get pods -n default
kubectl get services -n default
```

The Helm release should be named `monitoring` and should show `deployed`.
Prometheus and Grafana pods should show `Running`.

Useful Kubernetes terms:

- A **namespace** separates resources logically.
- A **service** provides a stable address for a pod or group of pods.
- A **Helm release** is an installed package managed by Helm.

## Open Prometheus

Create a temporary local connection to Prometheus:

```bash
kubectl port-forward \
  -n default \
  svc/monitoring-kube-prometheus-prometheus \
  9090:9090
```

Open `http://localhost:9090` in a browser. Test a simple query:

```promql
up
```

The value `1` normally means that a monitored target is reachable. PromQL is
the query language used by Prometheus.

## Open Grafana

In another terminal, create the Grafana connection:

```bash
kubectl port-forward \
  -n default \
  svc/monitoring-grafana \
  3000:80
```

Open `http://localhost:3000`. Log in with the `admin` user. The password comes
from a Kubernetes Secret and must not be committed to Git or included in a
screenshot.

In Grafana, confirm that the Prometheus data source is available. Then create
or open the `Kubernetes Operations Dashboard` documented in
`03-operational-dashboard.md`.

## Troubleshooting

If Prometheus shows no data:

```bash
kubectl get pods -n default
kubectl get servicemonitors -A
kubectl get prometheus -n default
```

If Grafana does not open, check that the port-forward terminal is still
running. A port-forward exists only while that command is active.

## Expected Result

At the end of this step:

- Prometheus opens at `http://localhost:9090`.
- The query `up` returns target status.
- Grafana opens at `http://localhost:3000`.
- Grafana can read metrics from Prometheus.
