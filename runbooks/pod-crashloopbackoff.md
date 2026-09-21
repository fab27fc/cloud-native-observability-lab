# Runbook - Pod CrashLoopBackOff

## Purpose

Investigate and validate a Kubernetes pod that is repeatedly crashing due to a
container exit code failure.

## Symptoms

```bash
kubectl get pods -n observability-demo -l app.kubernetes.io/name=crashloop-demo -o wide
```

Expected output:

```text
STATUS: CrashLoopBackOff
READY: 0/1
RESTARTS: increasing
```

## Investigation Steps

### 1. Check pod status

```bash
kubectl get pods -n observability-demo -l app.kubernetes.io/name=crashloop-demo -o wide
```

### 2. Inspect pod details

```bash
kubectl describe pod -n observability-demo -l app.kubernetes.io/name=crashloop-demo
```

Look for:

- `State: Waiting`
- `Reason: CrashLoopBackOff`
- `Last State: Terminated`
- `Reason: Error`
- `Exit Code: 1`

### 3. Review container logs

```bash
kubectl logs -n observability-demo -l app.kubernetes.io/name=crashloop-demo
kubectl logs -n observability-demo -l app.kubernetes.io/name=crashloop-demo --previous
```

Expected log output:

```text
Simulated failure
```

### 4. Review Kubernetes events

```bash
kubectl get events -n observability-demo --sort-by=.metadata.creationTimestamp
```

Look for:

- `BackOff`
- `Created`
- repeated restarts for the same container

## Root Cause

The pod exits immediately because the container command intentionally runs:

```sh
sh -c "echo 'Simulated failure'; exit 1"
```

This causes the container to fail fast and Kubernetes to restart it repeatedly.

## Remediation

1. Review the container command and exit behavior.
2. Confirm the application is expected to fail for the controlled exercise.
3. If this is an unintended issue, fix the startup command, image, or configuration.
4. If the issue is intentional, document the evidence and proceed to RCA.

## Related Files

- [kubernetes/crashloop-demo.yaml](../kubernetes/crashloop-demo.yaml)
- [incidents/INC-001-crashloopbackoff.md](../incidents/INC-001-crashloopbackoff.md)
- [docs/04-alerting.md](../docs/04-alerting.md)
