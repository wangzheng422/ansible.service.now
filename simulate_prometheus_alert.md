# Simulate Prometheus Alert with cURL

This document provides a `curl` command to simulate a Prometheus alert being sent to the webhook endpoint configured in the `prometheus_alert_rulebook.yml`. This can be used for testing the rulebook and the associated Ansible Automation Platform (AAP) workflow.

**Prerequisites:**

*   Your Event-Driven Ansible (EDA) instance with the `prometheus_alert_rulebook.yml` deployed and running.
*   The webhook listener in the rulebook is configured to listen on `http://0.0.0.0:5000`.

**cURL Command:**

Replace `YOUR_EDA_HOST` with the actual hostname or IP address where your EDA instance is running. If running locally, you can use `localhost`.

```bash
curl -X POST -H "Content-Type: application/json" \
-d '{
  "version": "4",
  "groupKey": "{}:{alertname=\"HostHighCpuUsage\"}",
  "truncatedAlerts": 0,
  "status": "firing",
  "receiver": "webhook",
  "groupLabels": {
    "alertname": "HostHighCpuUsage"
  },
  "commonLabels": {
    "alertname": "HostHighCpuUsage",
    "instance": "localhost:9100",
    "job": "node_exporter",
    "severity": "critical"
  },
  "commonAnnotations": {
    "summary": "Host localhost:9100 CPU usage is high"
  },
  "externalURL": "http://localhost:9090",
  "alerts": [
    {
      "status": "firing",
      "labels": {
        "alertname": "HostHighCpuUsage",
        "instance": "localhost:9100",
        "job": "node_exporter",
        "severity": "critical"
      },
      "annotations": {
        "summary": "Host localhost:9100 CPU usage is high",
        "description": "This is a simulated alert for high CPU usage on localhost:9100."
      },
      "startsAt": "2025-06-20T07:00:00.000Z",
      "endsAt": "0001-01-01T00:00:00.000Z",
      "generatorURL": "http://localhost:9090/graph?g0.expr=node_cpu_usage_total%7Bmode%3D%22idle%22%7D+%3C+10&g0.tab=1",
      "fingerprint": "abcdef1234567890"
    }
  ]
}' \
http://wzh-test-01-aap.apps.cluster-snw5f-1.dynamic.redhatworkshops.io


```

**Explanation of the cURL Command:**

*   `-X POST`: Specifies that this is a POST request.
*   `-H "Content-Type: application/json"`: Sets the `Content-Type` header to indicate that the request body is JSON.
*   `-d '...'`: Provides the JSON payload for the request. This payload mimics a typical Prometheus alert structure.
*   `http://YOUR_EDA_HOST:5000`: The target URL for the webhook endpoint.