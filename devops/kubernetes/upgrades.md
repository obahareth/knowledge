# Upgrades

We can upgrade by using `kubectl set image deployment/deployment_name helloworld=obahareth/new_hello_world`.

When we redeploy with a new image we get a new replicaset that matches the new set of images.

## Rollouts

We can view the rollout history by running `kubectl rollout history deployment/deployment_name`, we can use the revision numbers here to roll back to a specific version as well.

### Undoing

`kubectl rollout undo deployment/deployment_name`

This will roll back to the previous version

### Rolling Back to a Specific Version

`kubectl rollout undo deployment/deployment_name --to-revision=3`

This will roll back to revision 3.

