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
| E05 | Grafana operational dashboard | `07-kubernetes-operations-dashboard-final.png` | `screenshots/grafana/` |
| E06 | CrashLoopBackOff is visible | `08-crashloop-pod.png` | `screenshots/kubernetes/` |
| E07 | CrashLoopBackOff investigation evidence | `09-crashloop-describe.png` | `screenshots/kubernetes/` |
| E08 | CrashLoopBackOff logs and events | `06-crashloop-logs-events.png` | `screenshots/kubernetes/` |
| E09 | Datadog cluster overview | `11-datadog-cluster-overview.png` | `screenshots/datadog/` |
| E10 | Datadog Kubernetes pods and resources | `12-datadog-kubernetes-pods.png` | `screenshots/datadog/` |
| E11 | Datadog Kubernetes events | `14-datadog-kubernetes-events.png` | `screenshots/datadog/` |
| E12 | Datadog error event | `15-datadog-error-event.png` | `screenshots/datadog/` |
| E13 | Splunk Collector is running | `16-splunk-collector-daemonset.png` | `screenshots/splunk/` |
| E14 | Splunk HEC is receiving logs | `17-splunk-hec-logs.png` | `screenshots/splunk/` |
| E15 | Splunk logs searchable by pod | `18-splunk-search-by-pod.png` | `screenshots/splunk/` |
| E16 | Splunk logs searchable by namespace | `19-splunk-search-by-namespace.png` | `screenshots/splunk/` |

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

## Evidence Index

The files in the checklist above are the canonical evidence set for this
repository. Link to these files from incident reports or tool documentation;
do not create duplicate copies with alternate names.

Incident correlation:

- Kubernetes: `screenshots/kubernetes/08-crashloop-pod.png`,
	`screenshots/kubernetes/09-crashloop-describe.png`, and
	`screenshots/kubernetes/06-crashloop-logs-events.png`
- Grafana: `screenshots/grafana/07-kubernetes-operations-dashboard-final.png`
- Datadog: `screenshots/datadog/14-datadog-kubernetes-events.png` and
	`screenshots/datadog/15-datadog-error-event.png`
- Splunk: `screenshots/splunk/17-splunk-hec-logs.png`,
	`screenshots/splunk/18-splunk-search-by-pod.png`, and
	`screenshots/splunk/19-splunk-search-by-namespace.png`

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