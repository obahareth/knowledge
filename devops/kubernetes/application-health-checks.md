# Application Health Checks

## Readiness Probe

Used to know when a **container is ready to accept traffic**. It can be configured to wait for a number of seconds after pod startup before applying health checking, and with a number of seconds to time out.

Types of readiness probes:

- HTTP probe.

## Liveness Probe

Periodic check used be Kubernetes to make sure the **container is still healthy**.