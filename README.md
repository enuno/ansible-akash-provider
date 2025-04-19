# Ansible Akash Cloud Provider Deployment (Work In Progress)

## Project Overview
This Ansible project automates the deployment of an Akash Cloud Provider on Kubernetes, implementing best practices for blockchain node management, cluster orchestration, and provider services. The infrastructure-as-code solution ensures repeatable, secure deployments across environments.

**Note:** This playbook is still in the testing phases and is not production ready.

### Key Features

✅  **Full-stack deployment of Akash blockchain components**

✅  **Production-grade Kubernetes cluster provisioning**

✅  **Integrated TLS certificate management**

✅  **Persistent storage configuration**

✅  **IP leasing enablement**

✅  **Idempotent operations for reliable updates**

## Directory Structure

```
ansible-akash-provider/
├── ansible.cfg
├── inventories/
│   └── production/
│       ├── group_vars/
│       │   ├── akash_nodes.yml
│       │   ├── k8s_cluster.yml
│       │   └── providers.yml
│       └── hosts
├── roles/
│   ├── chrony/               # Time synchronization
│   ├── akash-node/           # Blockchain node deployment
│   ├── kubernetes/           # K8s cluster provisioning
│   ├── akash-provider/       # Core provider services
│   ├── tls-certs/            # Let’s Encrypt integration
│   ├── ip-leases/            # MetalLB configuration
│   └── persistent-storage/   # Ceph/Rook storage
├── playbooks/
│   ├── 01-akash-node.yml
│   ├── 02-k8s-cluster.yml
│   ├── 03-provider-core.yml
│   └── 04-provider-extensions.yml
└── vault/
    └── secrets.yml           # Encrypted credentials
```

## Prerequisites

1. Ansible 2.15+ with Python 3.11+
2. SSH access to target nodes with sudo privileges
3. DNS control for TLS certificate management
4. Kubernetes-ready infrastructure (VMs/Bare Metal)

## Installation

```bash
# Clone repository
git clone https://github.com/yourrepo/ansible-akash-provider
cd ansible-akash-provider

# Install collections
ansible-galaxy collection install kubernetes.core community.kubernetes

# Initialize vault
ansible-vault create vault/secrets.yml

## Inventory Configuration
inventories/production/hosts
```

```ini
[akash_nodes]
akash-node-01 ansible_host=192.168.1.50

[k8s_control_plane]
k8s-cp-01 ansible_host=10.0.0.10

[k8s_workers]
k8s-worker-01 ansible_host=10.0.0.11
k8s-worker-02 ansible_host=10.0.0.12
k8s-worker-03 ansible_host=10.0.0.13

[providers]
akash-provider-01 ansible_host=provider.example.com
```

## Group Variables Examples
inventories/production/group_vars/akash_nodes.yml

```yaml
akash_version: "0.19.7"
chain_id: "akashnet-2"
node_moniker: "akash-node-01"
```

## Playbook Execution Order

### 1. Blockchain Infrastructure

```bash
ansible-playbook -i production playbooks/01-akash-node.yml --vault-id @prompt
```

### 2. Kubernetes Cluster

```bash
ansible-playbook -i production playbooks/02-k8s-cluster.yml
```

### 3. Core Provider Services

```bash
ansible-playbook -i production playbooks/03-provider-core.yml --vault-id @prompt
```

### 4. Advanced Features

```bash
ansible-playbook -i production playbooks/04-provider-extensions.yml
```

## Playbook/Role Breakdown

### 1. 01-akash-node.yml

**Purpose:** Deploys Akash blockchain node

**Roles:**
- **chrony:** Ensures microsecond time synchronization
- **akash-node:** Installs & configures blockchain softwar

**Key Tasks:**
- NTP configuration with Chrony
- Akash binary installation
- Genesis file validation
- Systemd service creation

### 02-k8s-cluster.yml
**Purpose:** Provisions production Kubernetes cluster

**Role:**

- **kubernetes:** Base cluster installation

**Key Features:**
- Control plane initialization
- Worker node joining
- Network plugin (Calico) deployment
- Kernel parameter optimization

### 3. 03-provider-core.yml

**Purpose:** Deploys Akash provider services

**Role:**
- **akash-provider:** Main provider components

**Components:**
- Helm chart deployment
- Wallet key management
- Provider YAML configuration
- Health checks

### 4. 04-provider-extensions.yml

**Purpose:** Enhances provider capabilities

**Roles:**
- **tls-certs:** Automated Let’s Encrypt certificates
- **ip-leases:** MetalLB IP address management
- **persistent-storage:** Ceph/Rook storage

**Security:**
- TLS 1.3 encryption
- Certificate auto-renewal
- Storage class RBAC

## Role Function Matrix

| Role                  | Key Functionality                             | Dependencies                |
|-----------------------|-----------------------------------------------|-----------------------------|
| `chrony`              | NTP time sync (μs precision)                  | None                        |
| `akash-node`          | Blockchain node operations                    | Chrony                      |
| `kubernetes`          | Cluster provisioning & hardening              | Ansible.posix               |
| `akash-provider`      | Helm-based provider deployment                | Kubernetes.core             |
| `tls-certs`           | TLS certificate management                    | cert-manager                |
| `ip-leases`           | IP address allocation                         | MetalLB                     |
| `persistent-storage`  | Storage class configuration                   | Rook Ceph                   |

## Verification

```bash
# Check node sync status
akash status --node http://akash-node:26657

# Verify provider registration
akash query provider get $(akash keys show provider -a)

# Test deployment capability
akash market lease list
```

## Additional Notes

- **Security:** Always use Ansible Vault for sensitive data
- **Customization:** Modify group_vars for environment specifics
- **Monitoring:** Integrate Prometheus/Grafana post-deployment

## Support

Pull requests welcome. Please follow Ansible best practices and include documentation updates.

---

## Contributing

Pull requests welcome. Please follow Ansible best practices and include documentation updates.
