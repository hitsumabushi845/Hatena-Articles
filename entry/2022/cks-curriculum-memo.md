---
title: CKS Exam Curriculum v1.23 と KodeKloud『Kubernetes Certified Security Specialist (CKS)』の対応
date: 2022-02-17T01:45:55.000Z
categories:
  - Kubernetes
id: "13574176438064320792"
draft: false
---
[https://kodekloud.com/courses/certified-kubernetes-security-specialist-cks/:embed:cite]

CKA のときと同様に、KodeKloud の講座と 2022年2月現在利用されているカリキュラム、v1.23 との対応表を作成する。  
参照したカリキュラムは以下。

[https://github.com/cncf/curriculum/blob/master/CKS_Curriculum_%20v1.23.pdf:embed:cite]

講座を受けていく中で追加/変更が必要になった場合は適宜修正する。

# 10% - Cluster Setup

- Use Network security policies to restrict cluster access  
  https://kodekloud.com/topic/network-policy/  
  https://kodekloud.com/topic/developing-network-policies-3/
- Use CIS benchmark to review the security configuration of Kubernetes components(etcd, kubelet, kubedns, kubeapi)  
  https://kodekloud.com/topic/cis-benchmark-for-kubernetes/
- Properly set up Ingress objects with security control  
  https://kodekloud.com/topic/ingress-2/
- Protect node metadata and endpoints  
  https://kodekloud.com/topic/kubelet-security/
- Minimize use of, and access to, GUI elements  
  https://kodekloud.com/topic/securing-kubernetes-dashboard/
- Verify platform binaries before deploying  
  https://kodekloud.com/topic/verify-platform-binaries-before-deploying/

# 15% - Cluster Hardening

- Restrict access to Kubernetes API
- Use Role Based Access Controls to minimize exposure  
  https://kodekloud.com/topic/rbac/
- Exercise caution in using service accounts e.g. disable defaults, minimize permissions on newly created ones  
  https://kodekloud.com/topic/service-accounts/
- Update Kubernetes frequently  
  https://kodekloud.com/topic/cluster-upgrade-process/

# 15% - System Hardening

- Minimize host OS footprint(reduce attack surface)  
  https://kodekloud.com/topic/minimize-host-os-footprint-intro/
- Minimize IAM roles  
  https://kodekloud.com/topic/minimize-iam-roles/
- Minimize external access to the network  
  https://kodekloud.com/topic/minimize-external-access-to-the-network/
- Appropriately use kernel hardening tools such as AppArmor, seccomp  
  https://kodekloud.com/topic/apparmor-in-kubernetes/  
  https://kodekloud.com/topic/restrict-syscalls-using-seccomp/

# 20% - Minimize Microservice Vulnerabilities

- Setup appropriate OS level security domains e.g. using PSP, OPA, security contexts  
  https://kodekloud.com/topic/pod-security-policies/  
  https://kodekloud.com/topic/open-policy-agent-opa/  
  https://kodekloud.com/topic/security-contexts-4/
- Manage kubernetes secrets  
  https://kodekloud.com/topic/manage-kubernetes-secrets/
- Use container runtime sandboxes in multi-tenant environments(e.g. gvisor, kata containers)  
  https://kodekloud.com/topic/gvisor/  
  https://kodekloud.com/topic/kata-containers/
- Implement pod to pod encryption by use of mTLS  
  https://kodekloud.com/topic/implement-pod-to-pod-encryption-by-use-of-mtls/

# 20% - Supply Chain Security

- Minimize base image footprint  
  https://kodekloud.com/topic/minimize-base-image-footprint/
- Secure your supply chain: whitelist allowed image registries, sign and validate images  
  https://kodekloud.com/topic/whitelist-allowed-registries-image-policy-webhook/
- Use static analysis of user workloads(e.g. kubernetes resources, docker files)  
  https://kodekloud.com/topic/use-static-analysis-of-user-workloads-e-g-kubernetes-resources-docker-files/
- Scan images for known vulnerabilities  
  https://kodekloud.com/topic/scan-images-for-known-vulnerabilities-trivy/

# 20% - Monitoring, Logging and Runtime Security

- Perform behavioral analytics of syscall process and file activities at the host and container level to detect malicious activities  
  https://kodekloud.com/topic/perform-behavioral-analytics-of-syscall-process/
- Detect threats within physical infrastructure, apps, networks, data, users and workloads
- Detect all phases of attack regardless where it occurs and how it spreads  
- Perform deep analytical investigation and identification of bad actors within environment  
  https://kodekloud.com/topic/use-falco-to-detect-threats-2/
- Ensure immutability of containers at runtime  
  https://kodekloud.com/topic/ensure-immutability-of-containers-at-runtime/
- Use Audit Logs to monitor access  
  https://kodekloud.com/topic/use-audit-logs-to-monitor-access/

だいたいこんなところか。CKS 自体最近提供された試験であるため出題範囲に大幅な変更が行われていないこと、KodeKloud の講座も最近提供されたものであることから、講座がカリキュラムにわりと忠実に沿った内容であることが確認できた。進めていきましょう。
