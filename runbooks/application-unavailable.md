# Runbook - Application Unavailable

## Purpose

Investigate a demo application whose Kubernetes deployment does not have the
number of ready replicas that it should have.

## Simple Explanation

The `demo-app` deployment should run two copies of the application. Kubernetes
reports the application as unavailable when the number of ready copies is lower
than the requested number.

```text
Requested replicas: 2
Available replicas: 0 or 1
Application: unavailable
```

The Prometheus alert for this condition is named `ApplicationUnavailable`.

## Symptoms

The alert may fire with `critical` severity. The following command shows the
current pod status:

```bash
kubectl get pods -n observability-demo \
	-l app.kubernetes.io/name=demo-app -o wide
```

Expected healthy output:

```text
STATUS: Running
READY: 1/1
```

The deployment should also report two available replicas:

```bash
kubectl get deployment demo-app -n observability-demo
```

## Investigation Steps

### 1. Check deployment availability

```bash
kubectl get deployment demo-app -n observability-demo
kubectl describe deployment demo-app -n observability-demo
```

Compare these values:

- `DESIRED`: the number of replicas requested by the deployment;
- `CURRENT`: the number of pods created;
- `AVAILABLE`: the number of ready pods.

### 2. Check the pods

```bash
kubectl get pods -n observability-demo \
	-l app.kubernetes.io/name=demo-app -o wide
```

Look for:

- `Pending`: Kubernetes cannot schedule the pod;
- `ImagePullBackOff`: the image cannot be downloaded;
- `CrashLoopBackOff`: the container starts and then repeatedly fails;
- `0/1 Ready`: the readiness probe is failing.

### 3. Review pod details and logs

```bash
kubectl describe pod -n observability-demo \
	-l app.kubernetes.io/name=demo-app

kubectl logs -n observability-demo \
	-l app.kubernetes.io/name=demo-app --all-containers
```

Review the `Events` section in the description. It often explains image,
scheduling, volume, or probe problems.

### 4. Check the service endpoints

```bash
kubectl get service demo-app -n observability-demo
kubectl get endpoints demo-app -n observability-demo
```

The service should have pod addresses. If there are no endpoints, the service
selector does not match ready pods or the pods are not ready.

### 5. Check the application response

Create a temporary local connection to the service:

```bash
kubectl port-forward -n observability-demo svc/demo-app 8080:80
```

In another terminal, test it:

```bash
curl -I http://127.0.0.1:8080/
```

An HTTP `200 OK` response confirms that the demo web application is reachable.

## Common Causes

- A pod cannot be scheduled because of insufficient resources.
- The container image cannot be pulled.
- The container exits or enters `CrashLoopBackOff`.
- The readiness probe cannot reach the application.
- The service selector does not match the deployment labels.
- A configuration, secret, volume, or network dependency is missing.

## Remediation

1. Use `kubectl describe` and the pod events to identify the first failure.
2. Fix the image, configuration, resources, labels, or readiness probe.
3. Wait for Kubernetes to create replacement pods.
4. Confirm that all expected replicas are ready.
5. Test the service with `curl`.
6. Confirm that the `ApplicationUnavailable` alert returns to an inactive state.

For this lab, the healthy application definition is in:

```text
kubernetes/demo-app.yaml
```

## Related Files

- [kubernetes/demo-app.yaml](../kubernetes/demo-app.yaml)
- [prometheus/alert-rules.yaml](../prometheus/alert-rules.yaml)
- [docs/04-alerting.md](../docs/04-alerting.md)
