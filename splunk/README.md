# Splunk Logging Integration

## Purpose

This folder contains the configuration and notes for sending
Kubernetes container logs to Splunk Cloud through the Splunk OpenTelemetry
Collector and HTTP Event Collector (HEC).

## Current Status

Splunk has been validated in the Kubernetes environment. The Collector runs in
the `splunk-otel` namespace, forwards Kubernetes logs through HEC, and the
events are searchable in Splunk Cloud.

## Data Flow

```text
Kubernetes container logs -> Splunk OpenTelemetry Collector -> Splunk HEC
```

## Required Values

The installation requires:

- Splunk Cloud HEC endpoint;
- HEC token;
- Kubernetes namespace for the Collector;
- a Collector configuration with the Kubernetes metadata processor.

HEC tokens must be stored in a Kubernetes Secret and must never be committed
to Git. Validation evidence is stored in `screenshots/splunk/`.
