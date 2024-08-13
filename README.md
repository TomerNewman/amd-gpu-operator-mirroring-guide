# Guide for Mirroring AMD GPU Operator Images on OpenShift

## Prerequisites
1. Disconnected OpenShift single-node cluster.
2. An internal image registry, running on the same network subnet of the cluster node.
3. Push the following images to the registry:
	a.  quay.io/yshnaidm/node-exporter:latest.
	b.  quay.io/yshnaidm/amd-gpu-operator:latest.
	c. 
	
4. Mirror the OpenShift cluster's release image to the internal registry (can be found using `oc adm release info`).
5. The following should be already installed: `Kernel Module Management (KMM)`, `Node Feature Discovery (NFD)`, `AMD GPU operator`.


## Steps
1. Access the node using `oc debug node/<node_name>` . 
2. Use host binaries using `chroot /host`.
3. Add those configurations to `/etc/containers/registry.conf`:
4. Restart container runtime using `systemctl restart crio`.
5. Apply your `DeviceConfig` CR.


   
