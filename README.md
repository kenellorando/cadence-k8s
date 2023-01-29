# cadence-k8s

Kubernetes `kustomize` configuration for the components of a Cadence Radio stack: `cadence`, `cadence_icecast2`, and `cadence_liquidsoap`.

## Read First, Carefully
I run this on my home cluster for fun and it works, with some caveats:

1. Server-sent event connections (real-time now playing, album art) to the UI sometimes drop after a while.
2. Music storage is currently handled using local-storage persistent volumes. All Cadence components must run on the node with the volume. This also means you need to make your own PV available on a node before applying.

If you want to run a real production radio stack, please use Cadence's Docker Compose on a single node. If you don't have problems with this or just really love Cadence or Kubernetes, keep reading.

## Directory
```
base/ - Kubernetes resources defining generic Cadence Radio stack components. You aren't going to change anything here.
overlay/ - User-modifiable deployment resources subdivided by environment. An `example` is provided for you to copy and modify.
```

## Complete Usage Example

This example will create a new Cadence stack production environment, ready for public access. It assumes you have an nginx Ingress Controller installed.

1. Copy the four files from `overlay/example` into a new directory called `overlay/production`. Switch to `overlay/production` and make some modifications to each new file.
2. Modify `ingress.yaml`:
   1. If you have your own domain names, set the two new `hosts`. The first listed host is the address meant for Cadence UI, and the second host is the address meant for Cadence Icecast Stream.
3. Modify `configmap.yaml`:
   1. Change every instance of `hackme` to a secure password.
   2. If you changed domain names in step 2, change the `<hostname>` on line 53 to match the domain you set for Cadence Icecast.
4. By default, `persistentVolumeClaim.yaml` will create a 50MB storage block for music under the path `/clusterstorage/cadence/`. Change the storage amount if desired. **Before continuing, verify you've already made a local PV available on a node in your cluster.**
5. `kubectl kustomize .` to validate your configuration, then `kubectl apply -k .` to apply.
