# Lab Architecture

```text
Ubuntu Management 192.168.100.30
├── kubectl
├── Helm
└── Git
	│ administra
	▼
RHEL Kubernetes 192.168.100.20
├── Demo application
├── Prometheus
├── Grafana
├── Alertmanager
├── Datadog Agent
└── Splunk OpenTelemetry Collector
	│ logs
	▼
Splunk Cloud
```

## Responsibilities

The Ubuntu management server is the administrative workstation. The RHEL
server hosts Kubernetes workloads and the observability components.

Prometheus collects metrics from Kubernetes and the demo application. Grafana
visualizes those metrics, while Alertmanager routes alerts. Datadog provides
external infrastructure monitoring. The Splunk OpenTelemetry Collector ships
container logs to Splunk Cloud through HEC.

## Evidence Flow

```text
Kubernetes status
      │
      ├── Prometheus metric ──► Grafana dashboard
      ├── Kubernetes event ───► Datadog event
      └── Container log ──────► Splunk search
				     │
				     ▼
				 Root cause
```
