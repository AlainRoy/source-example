# Example Control Plane Source Repository

This repository contains all configurations needed to operate a `ControlPlane`
in an Upbound Space.

## Features

* Control Plane Source repositories can contain only Crossplane manifests.
* There is no need to declare explicit dependencies, i.e. Space controller will
  apply `Provider`s before `ProviderConfig`.
* Only YAML files are processed, you can have other types of files in this
  repository.
* A Control Plane Source is designed to contain manifests for a single control
  plane. You can have all the metadata that is specific to a single control plane.

## Getting Started

`ControlPlane` API is available only in Upbound Spaces. Prepare an Upbound Space
by following the instructions [here](https://github.com/upbound-demo/spaces-instructions).

Once you have the Space up and ready, create a `ControlPlane` referencing this
source.
```yaml
apiVersion: mxp.upbound.io/v1alpha1
kind: ControlPlane
metadata:
  name: example
spec:
  source:
    git:
      url: https://github.com/upbound/source-example
      ref:
        branch: main
  writeConnectionSecretToRef:
    name: kubeconfig-example
    namespace: upbound-system
```

Once the `ControlPlane` is in `Ready` state, it will start to pull manifests from
this repository. You can watch the progress with the following command:
```bash
kubectl describe controlplane example
```

You can also take a look at what's happening inside the control plane by using
its kubeconfig:
```bash
kubectl get secret kubeconfig-example -n upbound-system -o jsonpath='{.data.kubeconfig}' | base64 -d > /tmp/example.yaml
```
```bash
KUBECONFIG=/tmp/example.yaml kubectl get providers.pkg
```