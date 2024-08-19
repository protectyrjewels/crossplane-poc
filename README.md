# crossplane-poc

Just trying out [Crossplane](crossplane.io). Nothing to see here...

Using https://github.com/vfarcic/crossplane-kubernetes as a reference point.

## Start

Run `task bootstrap` and then `kubectl get pods -n crossplane-system`.

## Requirements

- [Taskfile](https://taskfile.dev)
- [helm](https://helm.sh)
- [kind](https://kind.sigs.k8s.io)
