# Guide for Mirroring AMD GPU Operator Images on OpenShift

## Prerequisites
1. Have a disconnected OpenShift single-node cluster.
2. Have an internal image registry, running on the same network subnet of the cluster node.
3. Push the following images to the registry:

	a.  `quay.io/yshnaidm/node-exporter:latest`.

	b.  `quay.io/yshnaidm/amd-gpu-operator:latest`.

	c.  `quay.io/yshnaidm/amd-gpu-operator:v0.0.4 (only from bundle)`.

	d.  `quay.io/yshnaidm/amd_gpu_sources:el9-6.1.1`.

	e.  `docker.io/rocm/k8s-device-plugin:labeller-latest`.

	f.  `docker.io/rocm/k8s-device-plugin:latest`.


 
4. Mirror the OpenShift cluster's release image to the internal registry (release image can be found using `oc adm release info`).
5. The following operators should be already installed: `Kernel Module Management (KMM)`, `Node Feature Discovery (NFD)`, `AMD GPU operator`.

---


## Steps
1. Access the node using `oc debug node/<node_name>` . 
2. Use host binaries using `chroot /host`.
3. Add the following configurations to `/etc/containers/registry.conf`:
```
[[registry]]
  prefix = ""
  location = "quay.io/yshnaidm"

  [[registry.mirror]]
    location = "<internal_registry_host>:<internal_registry_port>/yshnaidm"
    pull-from-mirror = "all"
   
[[registry]]
  prefix = ""
  location = "docker.io/rocm"

  [[registry.mirror]]
    location = "<internal_registry_host>:<internal_registry_port>/rocm"
    pull-from-mirror = "all"
```
   
5. Restart the container runtime using `systemctl restart crio`.
6. Apply your `DeviceConfig` CR.


   
