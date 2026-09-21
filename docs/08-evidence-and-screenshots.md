# 08 - Evidence and Screenshots

## Purpose

Capture reproducible evidence for each project requirement without exposing
passwords, API keys, tokens, kubeconfigs or other secrets.

## Screenshot Rules

- Capture only the relevant terminal or application panel.
- Include the command, namespace and resource name when visible.
- Do not include credentials, browser password fields, tokens or secret values.
- Use PNG format and descriptive lowercase names with numeric prefixes.
- Store screenshots under the matching directory in `screenshots/`.

## Evidence Checklist

| ID | Requirement | Suggested filename | Location |
|---|---|---|---|
| E01 | Kubernetes node is Ready | `01-nodes-ready.png` | `screenshots/kubernetes/` |
| E02 | Demo application is available | `02-demo-app-running.png` | `screenshots/kubernetes/` |
| E03 | Prometheus targets are healthy | `03-prometheus-targets.png` | `screenshots/prometheus/` |
| E04 | Grafana pods by phase panel | `04-grafana-pods-by-phase.png` | `screenshots/grafana/` |
| E05 | Grafana operational dashboard | `05-kubernetes-operations-dashboard.png` | `screenshots/grafana/` |
| E06 | CrashLoopBackOff is visible | `06-crashloop-pod.png` | `screenshots/kubernetes/` |
| E07 | Alert is firing or received | `07-alertmanager-crashloop.png` | `screenshots/prometheus/` |
| E08 | Datadog cluster and pod discovery | `08-datadog-kubernetes.png` | `screenshots/datadog/` |
| E09 | Datadog error event | `09-datadog-error-event.png` | `screenshots/datadog/` |
| E10 | Splunk collector is running | `10-splunk-collector.png` | `screenshots/splunk/` |
| E11 | Logs searchable in Splunk Cloud | `11-splunk-logs.png` | `screenshots/splunk/` |
| E12 | RCA evidence correlation | `12-incident-correlation.png` | `screenshots/` |

## Capture Commands

Run these commands from the management server and capture their output when
the corresponding resource exists:

```bash
kubectl get nodes -o wide
kubectl get pods -n observability-demo -o wide
kubectl get svc -n observability-demo
kubectl get events -n observability-demo --sort-by=.metadata.creationTimestamp
kubectl get pods -A | grep -Ei "prometheus|grafana|alertmanager|datadog|splunk"
```

For each screenshot, record the related command, timestamp, namespace and
what the evidence proves in the relevant document or incident report.

## Completion Criteria

The evidence set is complete when every requirement has at least one screenshot
or an explicitly documented reason why a screenshot is not applicable. The
screenshots must agree with the commands, dashboards, alerts and RCA.

## Next Capture Sequence

1. Capture the healthy cluster and demo application.
2. Capture Prometheus targets and individual Grafana panels.
3. Capture the completed Grafana dashboard.
4. Create the controlled `CrashLoopBackOff` incident.
5. Capture Kubernetes status, Prometheus alert, Datadog event and Splunk log.
6. Write the RCA using the evidence IDs above.