# Cloud-Native Observability Lab

Practical laboratory for observing a Kubernetes application with Prometheus,
Grafana, Alertmanager, Datadog and Splunk Cloud.

## Start Here

This project is written as a hands-on introduction. Follow the documents in
order and run the validation command after each step.

### The basic idea

- **Docker** packages an application and everything it needs into an image.
- A **container** is a running copy of an image.
- **Kubernetes** runs and manages containers.
- A **pod** is the smallest Kubernetes unit that runs containers.
- A **deployment** keeps the requested number of pods running.
- A **service** gives pods a stable network address.
- **Prometheus** stores metrics such as CPU usage and restart counts.
- **Grafana** turns metrics into dashboards.
- **Datadog** receives Kubernetes metrics, events, and logs.
- **Splunk** stores and searches application logs.

The basic flow is:

```text
Docker image -> container -> pod -> deployment -> service
                                      |
                                      +-> metrics -> Prometheus -> Grafana
                                      +-> events  -> Datadog
                                      +-> logs   -> Splunk
```

## Architecture

- Ubuntu Management `192.168.100.30`: runs Git, kubectl and Helm.
- RHEL Kubernetes `192.168.100.20`: hosts the demo application and the
  observability platform.

See the [detailed architecture](architecture/architecture.md) and the
[environment preparation guide](docs/01-environment.md).

## Objectives

- Deploy a demo application in Kubernetes.
- Build an operational dashboard in Grafana.
- Create alerts for `CrashLoopBackOff` and application unavailability.
- Send metrics and events to Datadog.
- Send logs through the Splunk OpenTelemetry Collector and HEC.
- Investigate a reproducible incident and document its root cause.

## Documentation

1. [Environment and requirements](docs/01-environment.md)
2. [Prometheus and Grafana](docs/02-prometheus-grafana.md)
3. [Operational dashboard](docs/03-operational-dashboard.md)
4. [Alerting](docs/04-alerting.md)
5. [Datadog](docs/05-datadog.md)
6. [Splunk Logging](docs/06-splunk-logging.md)
7. [Final validation](docs/07-final-validation.md)
8. [Evidence and screenshots](docs/08-evidence-and-screenshots.md)

## Security

Do not upload API keys, tokens, passwords, HEC tokens or kubeconfig files.
Use `REDACTED` in examples and keep real values only in the local environment
or a secrets manager.

## Lab Workflow

The laboratory starts with a healthy demo application and validates its pods,
service, logs, and metrics. A controlled failure is then introduced to
correlate Kubernetes status, Prometheus metrics, Grafana dashboards, Datadog
events, and Splunk logs with the final root-cause analysis.
