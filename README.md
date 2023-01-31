# cadence-k8s

Kubernetes `kustomize` configuration for the components of a Cadence Radio stack: `cadence`, `cadence_icecast2`, and `cadence_liquidsoap`.

> **Warning** Music storage is currently handled using local-storage persistent volumes. All Cadence components must run on the node with the volume. If you want to run a real production radio stack, I recommend you use Cadence's default Docker Compose method. If you don't have problems with this or just really love Cadence or Kubernetes, keep reading.

## Repository Organization
- `base/` Resources defining standard Cadence Radio stack components for a Kubernetes cluster. You aren't going to change anything here.
- `overlay/` - User-modifiable deployment resources subdivided by environment. Use these to customize the base Cadence deployment for your particular needs. An `example` environment is provided for you to copy and modify.

## Requirements

1. You have an nginx Ingress Controller installed to your cluster.
2. You have a local PV on one of your nodes.

## Deployment

1. Copy the four files from `overlay/example` into a new directory called `overlay/production`. Switch to `overlay/production` and make some modifications to each new file.
2. Modify `ingress.yaml`:
   1. If you have your own domain names, set the two new `hosts`. The first listed host is the address meant for Cadence UI, and the second host is the address meant for Cadence Icecast Stream.
3. Modify `configmap.yaml`:
   1. Change every instance of `hackme` to a secure password.
   2. If you changed domain names in step 2, change the `<hostname>` on line 53 to match the domain you set for Cadence Icecast.
4. By default, `persistentVolumeClaim.yaml` will create a 50MB storage block for music under the path `/clusterstorage/cadence/`. Change the storage amount if desired. Before continuing, verify you've already made a local PV available on a node in your cluster.
5. `kubectl kustomize .` to validate your configuration, then `kubectl apply -k .` to apply.

## Accessing Services

1. Point DNS for the hostnames you set in `ingress.yaml` to your cluster.
