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

- Kubernetes pod state and events ([pod status](../screenshots/kubernetes/08-crashloop-pod.png), [describe output](../screenshots/kubernetes/09-crashloop-describe.png), and [logs/events](../screenshots/kubernetes/06-crashloop-logs-events.png))
- Prometheus custom alert `KubernetesPodCrashLooping`
- Grafana dashboard panels for pod health and restarts ([dashboard](../screenshots/grafana/07-kubernetes-operations-dashboard-final.png))
- Datadog events and Kubernetes metadata ([Kubernetes events](../screenshots/datadog/14-datadog-kubernetes-events.png) and [error event](../screenshots/datadog/15-datadog-error-event.png))
- Splunk log collection and search ([HEC logs](../screenshots/splunk/17-splunk-hec-logs.png), [pod search](../screenshots/splunk/18-splunk-search-by-pod.png), and [namespace search](../screenshots/splunk/19-splunk-search-by-namespace.png))

## Conclusion

The container intentionally exited with code 1, causing a `CrashLoopBackOff`.
Kubernetes restarted the pod repeatedly, Prometheus fired the alert, Grafana
showed the issue, Datadog captured the event, and Splunk received the logs.
Evidence was saved in the required screenshot folders and no secrets were found
in the repository.
