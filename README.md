# 🛡️ Network Isolation & Jump-Box Configuration

![Virtualization: VirtualBox](https://img.shields.io/badge/Virtualization-Oracle_VM_VirtualBox-blue?style=for-the-badge&logo=virtualbox)
![OS: Ubuntu](https://img.shields.io/badge/OS-Ubuntu_24.04_LTS-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Assessment](https://img.shields.io/badge/Assessment-Ethical_Hacking_%2F_Networking-success?style=for-the-badge)

A complete demonstration of a secure, isolated private cluster accessed exclusively through a Gateway (Jump-Box). This setup simulates a real-world enterprise network environment where internal nodes are shielded from direct external access.

---

## 📋 1. Environment Overview

- **Virtualization:** Oracle VM VirtualBox
- **Operating System:** Ubuntu 24.04 LTS (Uniform across all VMs)
- **Minimum VM Resources:** 2 GB RAM, 1 CPU core per VM

## 🌐 2. Network Settings

- **Internal Network Name:** `private_cluster`
- **Internal Subnet:** `192.168.10.0/24` (Static addressing)
- **Network Type for `private_cluster`:** Internal Network (Fully isolated)

---

## 💻 3. VM Configuration & IP Assignments

| VM Name        | Role                   | Hostname  | Adapter 1                            | Adapter 2                            | IP Address(es)                                                 |
| :------------- | :--------------------- | :-------- | :----------------------------------- | :----------------------------------- | :------------------------------------------------------------- |
| **VM-Gateway** | Jump Box / Entry Point | `ubuntug` | Bridged or NAT (Internet)            | Internal Network (`private_cluster`) | `192.168.43.44` (Ext, DHCP)<br>`192.168.10.1/24` (Int, Static) |
| **VM-Node-A**  | Private Cluster Node   | `ubuntuf` | Internal Network (`private_cluster`) | —                                    | `192.168.10.2/24` (Static)                                     |
| **VM-Node-B**  | Private Cluster Node   | `ubuntuh` | Internal Network (`private_cluster`) | —                                    | `192.168.10.3/24` (Static)                                     |

---

## 🔧 4. Key Technical Details

### 🚪 Gateway (`ubuntug`):

- **Adapter 1** ➡️ Bridged or NAT network ➡️ Provides Internet access to the gateway.
  - **External IP:** `192.168.43.44` (DHCP from host/router network)
- **Adapter 2** ➡️ Internal Network `"private_cluster"`
  - **Static IP:** `192.168.10.1/24`
- Serves as the **only route** into the `192.168.10.0/24` private segment.

### 🔒 Nodes (`ubuntuf` & `ubuntuh`):

- Only **one adapter** attached to Internal Network `"private_cluster"`.
- **Static IPs configured:** `192.168.10.2/24` and `192.168.10.3/24`.
- No NAT, Bridged, or Host-Only adapter ➡️ **Fully isolated** from host & Internet.

---

## 5. Isolation Goal & Verification

**Goal:** Private nodes reachable **only** via the Gateway (jump-box). Direct access from host machine should fail.

### ✅ Verification Results

| Action                   | Source  | Target           | Result          |    Status     |
| :----------------------- | :------ | :--------------- | :-------------- | :-----------: |
| `ping 192.168.43.44`     | Host    | Gateway (Ext IP) | Succeeds        |      🟢       |
| `ping 192.168.10.2 / .3` | Host    | Nodes (Int IP)   | Fails (Timeout) | 🟢 (Expected) |
| `ping 192.168.10.2`      | Gateway | VM-Node-A        | Succeeds        |      🟢       |
| `ping 192.168.10.3`      | Gateway | VM-Node-B        | Succeeds        |      🟢       |
| `ssh ...@192.168.10.2`   | Gateway | VM-Node-A        | Works           |      🟢       |
| `ssh ...@192.168.10.X`   | Host    | Nodes            | Fails           | 🟢 (Expected) |

### 🛠️ Commands used for verification:

- `ip a` (On all VMs to verify interfaces and IPs)
- `ping <target-ip>` (From host and gateway to test connectivity)

---
<p align="right">
  <i><b>Created by Chirag</b></i>
</p>
