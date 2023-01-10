# cadence-k8s

Kubernetes `kustomize` configuration for the components of a Cadence Radio stack: `cadence`, `cadence_icecast2`, and `cadence_liquidsoap`.

## Read First: Known Issues
I run this on my home cluster for fun and it works, with some caveats:

1. Server-sent event connections to the UI sometimes drop after a while. Now playing information and album art might stop refreshing until client connection is reset.
2. Music storage with this configuration is currently handled using local-storage persistent volumes. Cadence components must run on the node with the volume.

If you want to run a real production radio stack, please use Cadence's Docker Compose on a single node. If you don't have problems with this or just really love Cadence or Kubernetes, keep reading.

## Directory
```
base/ - Kubernetes resources defining generic Cadence Radio stack components. You aren't going to change anything here.
overlay/ - User-modifiable deployment resources subdivided by environment. An example is provided for you to copy and modify.
```

## Complete Usage Example

This example will create a new Cadence stack environment named "production", provisioned with 50MB persistent volume for music, ready for public access.

1. Copy the four files from `overlay/example` into a new directory called `overlay/production`. Switch directory to `overlay/production` and make some modifications to each new file.
2. Modify `ingress.yaml`:
   1. If you have your own domain names, set the two new `hosts`. The first listed host is the address meant for the Cadence website, and the second host is the address meant for Cadence Icecast.
3. Modify `configmap.yaml`:
   1. Change every instance of `hackme` to a secure password.
   2. If you changed domain names in step 2, change the `<hostname>` on line 53 to match the domain you set for Cadence Icecast.
4. Modify `namespace.yaml` to set your namespace.
5. Modify `kustomize.yaml` to set the namespace to match what you set in step 4.
6. By default, `persistentVolume.yaml` and `persistentVolumeClaim.yaml` will create a 50MB storage block for music under the path `/clusterstorage/cadence/`. You must set the `HOSTNAME` in `persistentVolume.yaml` to the name of a cluster node where this music can be stored. You can change the storage amount and path as you see fit.
7. `kubectl kustomize .` to validate, then `kubectl apply -k .` to apply.
