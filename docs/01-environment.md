# 01 - Lab Environment and Requirements

## Objective

Prepare the Kubernetes environment required to build a cloud-native observability platform using Prometheus, Grafana, Alertmanager, Datadog, and Splunk.

## Before You Start: Docker and Kubernetes

Docker and Kubernetes solve different problems:

| Tool | Simple explanation |
|---|---|
| Docker | Builds and runs an application container. |
| Kubernetes | Keeps containers running and manages their network and resources. |

In this lab, the Kubernetes cluster already exists. The Ubuntu management
server controls it with `kubectl` and Helm, while the RHEL server hosts the
workloads.

The usual flow is:

1. An application is packaged as a Docker image.
2. Kubernetes starts that image inside a pod.
3. A deployment keeps the requested number of pods running.
4. A service gives the pods a stable network address.

You can think of `kubectl` as the command-line remote control for Kubernetes.
Most commands follow this pattern:

```text
kubectl <action> <resource> <optional-name-or-options>
```

For example, `kubectl get pods` means "show me the pods".

## Lab Architecture

The laboratory uses two Linux systems:

| System | IP Address | Purpose |
|---|---|---|
| LAB-UBU-MGMT-01 | 192.168.100.30 | Management server |
| LAB-RHEL-K8S-01 | 192.168.100.20 | Kubernetes cluster |

The Ubuntu management server is used to execute administrative commands. The RHEL server hosts the Kubernetes workloads and observability components.

## Requirements

### Management Server

The management server requires:

- Ubuntu Server
- Git
- kubectl
- Helm
- SSH access to the environment
- Network connectivity to the Kubernetes API
- A valid Kubernetes configuration file

Validate the required tools:

```bash
git --version
kubectl version --client
helm version
```

If one of these commands fails, install the missing tool before continuing.

### Kubernetes Cluster

The cluster requires:

- A Kubernetes control plane in the `Ready` state
- A working container runtime
- Kubernetes networking
- DNS resolution
- Persistent storage
- Access from the management server

Validate the cluster:

```bash
kubectl cluster-info
kubectl get nodes -o wide
kubectl get namespaces
kubectl get storageclass
kubectl get pvc -A
```

What these commands mean:

- `cluster-info`: confirms that the Kubernetes API is reachable.
- `get nodes`: shows the machines that can run workloads.
- `get namespaces`: shows the logical groups inside the cluster.
- `get storageclass`: shows available storage types.
- `get pvc -A`: shows storage requests in all namespaces.

The option `-A` means "all namespaces". Without it, Kubernetes checks only
the current namespace.

## Observability Components

This laboratory will use:

| Component | Purpose |
|---|---|
| Prometheus | Collect and store Kubernetes metrics |
| Grafana | Create operational dashboards |
| Alertmanager | Process and route alerts |
| kube-state-metrics | Expose Kubernetes object metrics |
| Node Exporter | Collect operating-system and node metrics |
| Datadog | Provide external infrastructure monitoring |
| Splunk | Centralize and search application logs |

Prometheus, Grafana, Alertmanager, kube-state-metrics, and Node Exporter are provided by the `kube-prometheus-stack` Helm chart.

Datadog and Splunk will be configured in later sections of this laboratory.

## Existing Monitoring Deployment

The monitoring platform is deployed in the `default` namespace with Helm.

Validate the release:

```bash
helm list -A
helm status monitoring -n default
```

Validate its workloads and services:

```bash
kubectl get pods -n default
kubectl get services -n default
kubectl get daemonsets -n default
```

Expected result:

- The `monitoring` Helm release shows `deployed`.
- Prometheus, Grafana and Alertmanager pods show `Running`.
- Node Exporter is running as a DaemonSet.
- The Prometheus PersistentVolumeClaim shows `Bound`.

## Configuration Backup

Before changing the monitoring platform, export its current Helm configuration:

```bash
helm get values monitoring \
	-n default \
	-o yaml > prometheus/values-current.yaml
```

Review the file before uploading it to GitHub:

```bash
grep -Ein \
	"password|token|api.?key|secret|credential" \
	prometheus/values-current.yaml
```

Passwords, API keys, tokens and credentials must be replaced with:

```text
REDACTED
```

## Accessing Grafana

Grafana is accessed securely through a temporary port-forward:

```bash
kubectl port-forward \
	-n default \
	svc/monitoring-grafana \
	3000:80
```

When using VS Code Remote SSH, forward port `3000` from the **Ports** tab.

Open Grafana locally:

```text
http://localhost:3000
```

Validate the connection:

```bash
curl -I http://127.0.0.1:3000/login
```

Expected result:

```text
HTTP/1.1 200 OK
```

The Grafana username is `admin`. The password is stored in a Kubernetes Secret and must not be included in the repository or screenshots.

## Environment Validation

The initial environment is ready when:

- The Kubernetes node is `Ready`.
- The monitoring Helm release is `deployed`.
- Prometheus, Grafana and Alertmanager pods are running.
- Prometheus storage is bound.
- Grafana opens at `http://localhost:3000`.
- No credentials are stored in the Git repository.

If a check fails, stop and fix that step before continuing. The error message
usually identifies the missing tool, connection, or Kubernetes resource.

## Next Step

Validate the connection between Grafana and Prometheus and confirm that Kubernetes metrics can be queried successfully.