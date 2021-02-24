# demo

Welcome to the **dannimal** workspace.

## How to deploy a workload using GitOps

Clone the workspace repository and commit and push your deployment.

```
git add deployment.yaml
git commit --message "Adds deployment"
git push
```

The cluster will momentarily sync any changes to the repo,
adding or removing resources as needed.

Make sure that the namespace of the deployment or any other resource in
git repository is a namespace managed by this workspace.

## Which namespaces does a Workspace manage?

These are set by the cluster admin and can be seen by getting the specific workspace object:

```
kubectl get workspace/demo -n wkp-workspaces -oyaml
```

## Accessing the workspace via kubectl

A cluster administrator can provide a _kubeconfig_ file to manage the resources
of a specific workspace. This file contains the credentials of the Workspace's
ServiceAccount that can be used by kubectl

```
kubectl --kubeconfig foo-workspace-kubeconfig get pods
```

The kubeconfig file sets the default namespace to the first namespace the
Workspace manages. Other namespaces can be accessed by using the namespace flag:
--namespace foo-namepspace-2.

## How to find git-sync/apply errors

A workspace repository shows in the status of the latest commit if the reconciliation succeeded.
Similar to github actions, it shows a green checkmark if it succeeded, or a red x icon with a brief
message if there was an error. To view more details on the reconciliation status and errors, please
follow the steps below.

See which git commit has been applied with

```
kubectl get gitrepository/demo -n wkp-workspaces
```

If any errors were found during application you can check with

```
kubectl get kustomizations/demo -n wkp-workspaces
```

## Notes

A current limitation of the Workspace Controller is its inability to apply an _empty_
repository to a cluster. If you have deployments and other manifests commited to this repository,
and then _delete all of them_ so there are 0 manifests left, then the apply will fail and the
resources will not be removed from the cluster. You can either:

1. Ask your admin to delete the workspace if you are done with it.
2. Add a dummy *ConfigMap* after deleting everything else so that you have at least 1 manifest to apply.
