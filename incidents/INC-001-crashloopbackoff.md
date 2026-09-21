# INC-001 - Controlled CrashLoopBackOff Incident

## Summary

A controlled `CrashLoopBackOff` was generated in the `observability-demo`
namespace to validate Kubernetes failure detection, Prometheus alerting,
Grafana visibility, and log correlation.

## Timeline

- Deployment applied: `kubernetes/crashloop-demo.yaml`
- Namespace: `observability-demo`
- Pod name: `crashloop-demo-6bc5dc5767-qdd7c`
- Initial status: `Error`
- Final state: `CrashLoopBackOff`

## Evidence

### Kubernetes status

```bash
kubectl get pods -n observability-demo -l app.kubernetes.io/name=crashloop-demo -o wide
```

Observed result:

```text
NAME                              READY   STATUS             RESTARTS        AGE
crashloop-demo-6bc5dc5767-qdd7c   0/1     CrashLoopBackOff   960 (90s ago)   3d9h
```

### Describe output

```bash
kubectl describe pod -n observability-demo -l app.kubernetes.io/name=crashloop-demo
```

Key evidence:

- `State: Waiting`
- `Reason: CrashLoopBackOff`
- `Last State: Terminated`
- `Reason: Error`
- `Exit Code: 1`
- `Restart Count: 960`

### Log output

```bash
kubectl logs -n observability-demo -l app.kubernetes.io/name=crashloop-demo
```

Observed result:

```text
Simulated failure
```

### Events

```bash
kubectl get events -n observability-demo --sort-by=.metadata.creationTimestamp
```

Relevant event:

```text
Warning  BackOff  ...  kubelet  Back-off restarting failed container crashloop-demo
```

## Root Cause

The container command deliberately exits with code 1:

```yaml
command:
  - sh
  - -c
  - echo "Simulated failure"; exit 1
```

This causes the container to terminate immediately, Kubernetes to restart it,
and the pod to enter a repeated restart loop. The behavior matches the expected
`CrashLoopBackOff` pattern used to validate alerting and investigation flow.

## Correlation

This incident was correlated with:

- Kubernetes pod state and events
- Prometheus custom alert `KubernetesPodCrashLooping`
- Grafana dashboard panels for pod health and restarts
- Datadog events and Kubernetes metadata (later section)
- Splunk log collection (later section)

## Conclusion

The incident was caused by the intentionally failing container command and
confirmed by pod description, events, and log output. The failure is fully
reproducible and suitable for RCA and alert validation.
