# Guide for Mirroring AMD GPU Operator Images on OpenShift

## Prerequisites
1. Have a disconnected OpenShift single-node cluster.
2. Have an internal image registry, running on the same network subnet of the cluster node.
3. Push the following images to the registry:

	a.  `quay.io/yshnaidm/node-exporter:latest`.

	b.  `quay.io/yshnaidm/amd-gpu-operator:latest`.

	c.  `quay.io/yshnaidm/amd-gpu-operator:v0.0.4 (only from bundle)`.

	d.  `docker.io/rocm/k8s-device-plugin:labeller-latest`.

	e.  `docker.io/rocm/k8s-device-plugin:latest`.

 
4. Mirror the OpenShift cluster's release image to the internal registry (release image can be found using `oc adm release info`).

5. The following operators should be already installed: `Kernel Module Management (KMM)`, `Node Feature Discovery (NFD)`, `AMD GPU operator`.

---


## Steps
1. Apply the following yaml:
```
apiVersion: operator.openshift.io/v1alpha1
kind: ImageContentSourcePolicy
metadata:
  name: mirror-to-internal-reg
spec:
  repositoryDigestMirrors:
  - mirrors:
    - <Internal_registry_name>:<Port>
    source: quay.io/yshnaidm
  - mirrors:
      - <Internal_registry_name>:<Port>
    source: docker.io/rocm
  - mirrors:
      - <Internal_registry_name>:<Port>
    source: registry.redhat.io/ubi9
```
#### Notice
- If there are base images that are in the Dockerfile of the Build yaml, they should be in the ImageContentSourcePolicy resource, at the same format as the following mirror rules.
- Each of the images that are mentioned in the Dockerfile, should be written there with digest instead of tag, in order for the ICSP mirroring to work properly.

2. Access the node using `oc debug node/<node_name>` .

3. Use host binaries using `chroot /host`.

4. Change `pull-from-mirror` to `all` in `/etc/containers/registry.conf` for the following rules only:

```
[[registry]]
  prefix = ""
  location = "docker.io/rocm"

  [[registry.mirror]]
    location = "<Internal_registry_name>:<Port>"
    pull-from-mirror = "all"

```
```
[[registry]]
  prefix = ""
  location = "quay.io/yshnaidm"

  [[registry.mirror]]
    location = "<Internal_registry_name>:<Port>"
    pull-from-mirror = "all"

```
   
5. Restart the container runtime using `systemctl restart crio`.

6. Apply your `DeviceConfig` CR.

