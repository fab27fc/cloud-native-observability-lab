# Splunk Logging Integration

## Purpose

This folder contains the planned configuration and notes for sending
Kubernetes container logs to Splunk Cloud through the Splunk OpenTelemetry
Collector and HTTP Event Collector (HEC).

## Current Status

Splunk is not installed in the Kubernetes cluster yet. There is currently no
Splunk namespace, Collector pod, Helm release, or HEC configuration in this
repository.

Do not create a fake screenshot or claim that logs were received until the
Collector and Splunk search have been validated.

## Planned Flow

```text
Kubernetes container logs -> Splunk OpenTelemetry Collector -> Splunk HEC
```

## Required Values

The future installation will require:

- Splunk Cloud HEC endpoint;
- HEC token;
- Kubernetes namespace for the Collector;
- a Collector configuration with the Kubernetes metadata processor.

HEC tokens must be stored in a Kubernetes Secret and must never be committed
to Git.
