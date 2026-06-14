🏗️ Cluster Architecture
Node	Role	IP	OS	Runtime
k8s-master-1	control-plane	192.168.77.132	Rocky Linux 9	containerd
k8s-worker-1	worker	192.168.77.131	Rocky Linux 9	containerd
k8s-worker-2	worker	192.168.77.133	Rocky Linux 9	containerd


🧩 Components Installed
Kubernetes v1.30.x

containerd runtime

Calico CNI (BGP mode)

kube-proxy (iptables mode)

CoreDNS

etcd (local to master)

🚀 Cluster Installation Guide
1️⃣ System Preparation
Disable swap
bash
swapoff -a
sed -i '/swap/d' /etc/fstab
Load kernel modules
bash
cat <<EOF | tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

modprobe overlay
modprobe br_netfilter
Sysctl settings
bash
cat <<EOF | tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sysctl --system
2️⃣ Install containerd
bash
dnf install -y containerd
containerd config default > /etc/containerd/config.toml
systemctl restart containerd
systemctl enable containerd
3️⃣ Install Kubernetes components
bash
dnf install -y kubeadm kubelet kubectl
systemctl enable kubelet
4️⃣ Initialize the Control Plane
bash
kubeadm init --pod-network-cidr=192.168.0.0/16
Configure kubectl for the root user:

bash
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
5️⃣ Install Calico CNI
bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.3/manifests/calico.yaml
6️⃣ Join Worker Nodes
bash
kubeadm join <MASTER-IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
🌐 Networking Overview
Pod CIDR: 192.168.0.0/16

Worker‑1 PodCIDR: 192.168.1.0/24

Worker‑2 PodCIDR: 192.168.2.0/24

Calico: BGP mesh enabled

kube-proxy: iptables mode

🛠️ Troubleshooting Commands
Check node status
bash
kubectl get nodes -o wide
Check Calico pods
bash
kubectl get pods -n kube-system -l k8s-app=calico-node -o wide
Check CNI interfaces
bash
ip a | grep cali
ip a | grep cni
Check BGP sessions
bash
calicoctl node status
🎓 CKA Training Modules
This repo includes hands‑on exercises for all CKA domains:

✔ Core Concepts
Pods, Deployments, ReplicaSets

YAML speed drills

Labels, selectors, annotations

✔ Scheduling
Taints & tolerations

Node selectors

Affinity & anti‑affinity

✔ Networking
Services (ClusterIP, NodePort)

NetworkPolicies

DNS debugging

✔ Storage
PV, PVC, StorageClasses

StatefulSets

Volume troubleshooting

✔ Troubleshooting
CrashLoopBackOff

Node NotReady

CNI failures

kube-proxy failures

etcd issues

✔ Cluster Maintenance
Draining nodes

Upgrading clusters

Backing up & restoring etcd

📂 Repository Structure
Code
kuberneteslab/
│
├── README.md
│
├── cluster-setup/
│   ├── master-install.md
│   ├── worker-install.md
│   ├── networking.md
│   └── calico-notes.md
│
├── manifests/
│   ├── deployments/
│   ├── services/
│   ├── networkpolicies/
│   └── storage/
│
├── training/
│   ├── CKA-01-core-concepts.md
│   ├── CKA-02-scheduling.md
│   ├── CKA-03-networking.md
│   ├── CKA-04-storage.md
│   ├── CKA-05-troubleshooting.md
│   └── scenarios/
│       ├── broken-pod.md
│       ├── node-notready.md
│       ├── cni-failure.md
│       └── kube-proxy-failure.md
│
└── scripts/
    ├── reset-node.sh
    ├── join-worker.sh
    └── troubleshoot.sh
