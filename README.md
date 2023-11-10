# cadence-k8s


Kubernetes `kustomize` configuration for the components of a Cadence Radio stack: `cadence`, `cadence_icecast2`, and `cadence_liquidsoap`.

> **Warning**: Currently out of date. This project supports Cadence Radio stacks running at `5.0.2`. It does not have support for more recent updates which use Redis, Postgres, and an nginx reverse-proxy within the stack.

> **Warning**: Music storage is currently supported through local-storage persistent volumes. All Cadence components must run on the node with the volume. 

## Repository Organization
- `base/` Manifests defining the standard Cadence Radio stack components. You aren't going to change anything here.
- `overlay/` - User-modifiable deployment resources subdivided by environment. Use these to customize the base Cadence deployment for your particular needs. An `example` environment is provided for you to copy and modify.

Basically, deployment with cadence-k8s takes the `base/` content, layers your `overlay/<env>` content on top of it, then launches the stack.

## Requirements

1. You have an nginx Ingress Controller installed to your cluster.
2. You have a local PV on one of your nodes.

## Deployment

1. Copy `overlay/example` into a new directory called `overlay/<env>`.
2. Switch to `overlay/<env>` and make some modifications to these files.
- `ingress.yaml`: If you have your own domain names, set them as the two `hosts`. The first listed host is the address meant for Cadence UI, and the second host is the address meant for Cadence Icecast Stream.
- `configmap.yaml`: Change every instance of `hackme` to a secure password. If you changed domain names in `ingress.yaml` earlier, change the `<hostname>` on line 53 to match the domain you set for Cadence Icecast.
- `persistentVolumeClaim.yaml`: By default, this file is configured to create a 50MB storage block for music under the path `/clusterstorage/cadence/`. Change the storage amount if desired.
3. Run `kubectl kustomize .` to validate your configuration, then `kubectl apply -k .` to apply.

## Accessing Services

1. Point DNS for the hostnames you set in `ingress.yaml` to your cluster.
