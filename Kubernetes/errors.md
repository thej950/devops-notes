## 📌 Main Points

### 1. **Pod & Container Failures**
- Common errors: `CrashLoopBackOff`, `RunContainerError`, `OOMKilled`.  
- Causes: app crashes, misconfigured entrypoints, memory limits exceeded.  
- Fixes: check logs, correct configs, adjust resource limits.

### 2. **Image & Registry Issues**
- Errors: `ImagePullBackOff`, `ErrImagePull`, `InvalidImageName`.  
- Causes: wrong image names, missing credentials, malformed references.  
- Fixes: verify image URLs, registry secrets, use versioned tags.

### 3. **Configuration Errors**
- Errors: invalid env/config, missing `ConfigMap` or `Secret`.  
- Causes: misconfigured references or missing resources.  
- Fixes: correct references, recreate missing resources.

### 4. **Scheduling & Resource Problems**
- Errors: `PodUnschedulable`, `FailedScheduling`, `NodeNotReady`.  
- Causes: insufficient resources, taints, node failures.  
- Fixes: adjust node pools, affinities, quotas, restart nodes.

### 5. **Storage & Volume Issues**
- Errors: `PVC Pending`, `VolumeMountError`, `Multi-Attach error`.  
- Causes: no matching PVs, incorrect mount paths, conflicting volume usage.  
- Fixes: provision PVs, check access modes, detach conflicting volumes.

### 6. **Networking & DNS Failures**
- Errors: service unreachable, DNS resolution failed, Ingress 404.  
- Causes: misconfigured selectors, CoreDNS crashes, wrong Ingress paths.  
- Fixes: correct service specs, restart DNS, fix Ingress rules.

### 7. **Security & Access Problems**
- Errors: RBAC denied, unauthorized, PodSecurityPolicy violation.  
- Causes: insufficient permissions, policy conflicts.  
- Fixes: add proper roles, renew credentials, adjust policies.

### 8. **Deployment & Scaling Issues**
- Errors: rollout stuck, HPA not scaling, Helm release failed.  
- Causes: misconfigured probes, missing metrics-server, Helm chart errors.  
- Fixes: validate templates, install metrics-server, fix probes.

### 9. **Cluster & Node Failures**
- Errors: node disk full, API server unreachable, kube-proxy fails.  
- Causes: resource exhaustion, network/cert issues, corrupted iptables.  
- Fixes: clean logs, check control plane, restore iptables.

### 10. **Miscellaneous Errors**
- Errors: invalid YAML, job deadline exceeded, pod never terminates.  
- Causes: syntax errors, logic flaws, missing timeouts.  
- Fixes: lint YAML, optimize job logic, add termination checks.

---

## 🎯 Key Takeaway
This document is a **Kubernetes troubleshooting handbook**:  
- Maps **errors → root causes → fixes**.  
- Emphasizes checking logs/configs first.  
- Validating resource references (images, secrets, ConfigMaps).  
- Managing cluster resources (nodes, memory, storage).  
- Ensuring networking, RBAC, and policies are correct.  
- Following best practices like versioned images, quotas, and monitoring tools.

---

- **k8s all errors list**[[click](https://drive.google.com/file/d/1u4Kyd5G8l6aCfMrrE8NI0XtKnA8__dNe/view?usp=sharing)]