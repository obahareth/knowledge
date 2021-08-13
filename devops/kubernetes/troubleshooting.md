# Troubleshooting

* Describe the deployment, `kubectl describe deployment bad_deployment`.
* Decribe a pod, `kubectl describe pod bad_pod`.
* Get the logs for a pod `kubectl logs pod_name`.
* Exec into the pod, `kubectl exec -it pod_name /bin/bash` \(If you have multiple containers in the pod use `-c container_name` to select the desired container\)

