# kuberneteslab
A test lab to test various kubernetes configurations 

Kubernetes Lab & CKA Training Environment
A fully‑documented Kubernetes cluster built on Rocky Linux 9, using kubeadm, containerd, and Calico.
This environment is designed for CKA exam preparation, troubleshooting practice, and real‑world cluster operations.

🖥️ Cluster Architecture
Node	Role	IP	OS	Runtime
k8s-master-1	control-plane	192.168.77.132	Rocky 9	containerd
k8s-worker-1	worker	192.168.77.131	Rocky 9	containerd
k8s-worker-2	worker	192.168.77.133	Rocky 9	containerd


🧩 Components Installed
Kubernetes v1.30.x

containerd runtime

Calico CNI (BGP mode)

kube-proxy (iptables mode)

CoreDNS

etcd (local to master)

🚀 Cluster Installation Steps
1. Disable swap
bash
swapoff -a
sed -i '/swap/d' /etc/fstab
2. Load required kernel modules
bash
cat <<EOF | tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

modprobe overlay
modprobe br_netfilter
3. Configure sysctl
bash
cat <<EOF | tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sysctl --system
4. Install containerd
bash
dnf install -y containerd
containerd config default > /etc/containerd/config.toml
systemctl restart containerd
systemctl enable containerd
5. Install kubeadm, kubelet, kubectl
bash
dnf install -y kubeadm kubelet kubectl
systemctl enable kubelet
6. Initialize the control plane
bash
kubeadm init --pod-network-cidr=192.168.0.0/16
7. Install Calico
bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.3/manifests/calico.yaml
8. Join workers
bash
kubeadm join <MASTER-IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
🛰️ Networking Overview
Pod CIDR: 192.168.0.0/16

Worker‑1 PodCIDR: 192.168.1.0/24

Worker‑2 PodCIDR: 192.168.2.0/24

Calico BGP mesh enabled

kube-proxy in iptables mode

🛠️ Troubleshooting Notes
Check Calico status
bash
kubectl get pods -n kube-system -l k8s-app=calico-node -o wide
Check BGP sessions
bash
calicoctl node status
Check CNI interfaces
bash
ip a | grep cali
ip a | grep cni
🎓 CKA Training Tasks Included
✔ Workloads & Scheduling
Create Deployments, DaemonSets, Jobs, CronJobs

Practice rolling updates & rollbacks

✔ Cluster Troubleshooting
Fix broken Pods

Debug CNI issues

Repair kube-proxy

Node NotReady scenarios

✔ Networking
NetworkPolicies

Services (ClusterIP, NodePort, LoadBalancer)

DNS debugging

✔ Storage
PersistentVolumes

StorageClasses

PVC binding

✔ Cluster Maintenance
Drain/uncordon nodes

Upgrade Kubernetes

Backup/restore etcd

📂 Repository Structure
Code
k8s-lab/
│
├── README.md
├── manifests/
│   ├── deployments/
│   ├── services/
│   ├── networkpolicies/
│   └── storage/
│
├── scripts/
│   ├── reset-node.sh
│   ├── join-worker.sh
│   └── troubleshoot.sh
│
└── training/
    ├── cka-tasks.md
    ├── troubleshooting-scenarios.md
    └── networking-labs.md
🏁 Next Steps
Add diagrams (architecture, networking, CNI flow)

Add automation scripts

Add your CKA practice tasks

Push everything to GitHub
