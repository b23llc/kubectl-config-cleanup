## kubectl-cleanup ##

kubectl cleanup is a plugin for automatically cleaning up your kubeconfig.  Every cloud provider has their own utilities for adding kubernetes cluster credentials to your kubeconfig but they don't offer the ability to clean it up once the cluster is deleted.
For those of us who launch and delete multiple clusters per day, it would be useful to have an automated way to clean up old kubeconfig entries. This plugin will attempt to connect to each cluster defined in a context, if the connection succeeds then the user, cluster, and context entry are maintained in the result. Otherwise, the entries are removed.

```bash
# prints the cleaned kubeconfig to stdout, similar to running: kubectl config view
kubectl cleanup

# cleanup and save the result back to the config file
kubectl cleanup --save

# cleanup and print the configs that were removed
kubectl cleanup --print-removed --raw > ./kubeconfig-removed.yaml

# print only the context names that would be removed during cleanup
kubectl cleanup --print-removed -o=jsonpath='{ range.contexts[*] }{ .name }{"\n"}'
```


### cleanup.ignore ###

Add a `~/.kube/cleanup.ignore` to specify contexts which should be ignored during cleanup. The associated context, user, and cluster
will be maintained in the output. This is useful for long running clusters where the api server is behind a firewall.

example:

```yaml
---
apiVersion: v1
kind: ConfigMap
data:
  contexts: |
    prod-cluster
    staging-cluster
    docker-for-desktop
```


### Build ###

go build cmd/kubectl-cleanup.go

### Install ###

mv kubectl-cleanup /usr/local/bin/.

> Requires `kubectl > v1.12.0`
https://kubernetes.io/docs/tasks/extend-kubectl/kubectl-plugins/#before-you-begin

> NOTE: cleanup does not support merging kubeconfig files: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/#the-kubeconfig-environment-variable
