# Azure Cloud Infrastructure Lab

Production-ready IaaS deployment demonstrating cloud infrastructure design, network segmentation, backup strategy, and cost management on Microsoft Azure.

![Network Topology](https://github.com/lionelmsango/Azure-Cloud-Infrastructure-Lab/blob/667c875b1a14da5933d27d4e7f32ce19c2e490af/network-topology.svg.svg)

## Project Overview

This project implements a secure, scalable cloud infrastructure for a fictional German SME (CloudTech Solutions GmbH) migrating their application server to Azure. The infrastructure follows Azure best practices for network segmentation, security hardening, and disaster recovery.

**Scenario:** The company needs to deploy a Windows Server-based application in the cloud with proper network isolation, backup protection, and cost controls.

## Infrastructure Components

### Network Architecture
- **Virtual Network:** `vnet-lionelcloudtech-prod` (10.1.0.0/16)
- **Subnet:** `snet-appservers` (10.1.1.0/24)
- **Network Security Group:** `nsg-appservers` with restricted RDP access
- **Region:** West Europe

### Compute Resources
- **Virtual Machine:** `vm-appserver01`
  - OS: Windows Server 2022 Datacenter (Azure Edition)
  - Size: Standard B2s (2 vCPUs, 8 GiB RAM)
  - IP: Private 10.1.1.4, Public 20.50.192.75
  - Auto-shutdown configured for 19:00 daily

### Storage
- **Storage Account:** `stlionelcloudtechprod`
  - Redundancy: Locally-redundant storage (LRS)
  - Blob container: `application-data`
  - Soft delete enabled (7-day retention)

### Backup & Recovery
- **Recovery Services Vault:** `rsv-lionelcloudtech-backup`
  - Daily backup policy (22:00)
  - 30-day retention period
  - First backup completed successfully

### Cost Management
- **Budget:** $200 USD with alerts at 50%, 75%, 90%
- **Actual spend:** <€0.01 (through VM deallocation strategy)
- **Monitoring:** Daily cost analysis with resource group filtering

## Technical Implementation

### Network Security

The NSG implements a least-privilege access model:

```
Priority: 100
Name: Allow-RDP-From-Admin
Source: <Admin IP>/32
Destination: Any
Port: 3389 (RDP)
Protocol: TCP
Action: Allow
```

All other inbound traffic is denied by default. This configuration allows remote administration only from the designated admin workstation while blocking unauthorized access attempts.

### IP Addressing Scheme

The address space follows a structured subnetting approach:

- **VNet:** 10.1.0.0/16 (65,536 addresses)
- **App Servers Subnet:** 10.1.1.0/24 (251 usable addresses)
- **Reserved for future subnets:** 10.1.2.0/24 through 10.1.255.0/24

This design allows for future expansion with additional subnets for database servers, management systems, or DMZ without requiring VNet reconfiguration.

### Connectivity Testing

Verified VM connectivity to Azure Storage using PowerShell:

```powershell
Test-NetConnection -ComputerName stlionelcloudtechprod.blob.core.windows.net -Port 443
```

Result: `TcpTestSucceeded : True` - confirming VM can access Azure Storage over HTTPS for application data and backups.

### Cost Optimization Strategy

The VM is configured with auto-shutdown at 19:00 (West Europe time) and manual start each morning. This deallocation strategy reduces compute costs by approximately 95% compared to 24/7 operation:

- **Running:** ~$1.50/day
- **Stopped (deallocated):** ~$0.10/day
- **Monthly savings:** ~$40

Total project cost (including deployment, testing, and documentation): Less than €1.

## Screenshots

### Infrastructure Setup

<table>
  <tr>
    <td width="50%">
      <img src="screenshots/01_Resource_Group_Created_png.jpg" alt="Resource Group" />
      <p align="center"><em>Resource group organization</em></p>
    </td>
    <td width="50%">
      <img src="screenshots/02_Virtual_Network_Created_png.jpg" alt="Virtual Network" />
      <p align="center"><em>VNet with segmented subnet</em></p>
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img src="screenshots/03_NSG_With_RDP_Rule_png.jpg" alt="NSG Rules" />
      <p align="center"><em>Firewall rules (NSG)</em></p>
    </td>
    <td width="50%">
      <img src="screenshots/04_VM_Deployment_Complete_png.jpg" alt="VM Deployment" />
      <p align="center"><em>Successful VM deployment</em></p>
    </td>
  </tr>
</table>

### Compute & Connectivity

<table>
  <tr>
    <td width="50%">
      <img src="screenshots/05_VM_Overview_Running_png.jpg" alt="VM Running" />
      <p align="center"><em>VM running (B2s configuration)</em></p>
    </td>
    <td width="50%">
      <img src="screenshots/06_RDP_Connected_To_VM_png.jpg" alt="RDP Connection" />
      <p align="center"><em>Remote desktop access verified</em></p>
    </td>
  </tr>
</table>

### Storage Configuration

<table>
  <tr>
    <td width="50%">
      <img src="screenshots/07_Storage_Account_Created_png.jpg" alt="Storage Account" />
      <p align="center"><em>LRS storage account</em></p>
    </td>
    <td width="50%">
      <img src="screenshots/08_Blob_Container_Created_png.jpg" alt="Blob Container" />
      <p align="center"><em>Application data container</em></p>
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img src="screenshots/09_Blob_File_Uploaded_png.jpg" alt="File Upload" />
      <p align="center"><em>Test file uploaded to blob storage</em></p>
    </td>
    <td width="50%">
      <img src="screenshots/10_Recovery_Vault_Created_png.jpg" alt="Recovery Vault" />
      <p align="center"><em>Backup vault configured</em></p>
    </td>
  </tr>
</table>

### Backup & Cost Management

<table>
  <tr>
    <td width="50%">
      <img src="screenshots/11_VM_Backup_Configured_png.jpg" alt="Backup Config" />
      <p align="center"><em>VM backup policy applied</em></p>
    </td>
    <td width="50%">
      <img src="screenshots/12_Backup_Job_In_Progress_png.jpg" alt="Backup Job" />
      <p align="center"><em>Initial backup job running</em></p>
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img src="screenshots/13_Cost_Analysis_View_png.jpg" alt="Cost Analysis" />
      <p align="center"><em>Cost tracking by resource group</em></p>
    </td>
    <td width="50%">
      <img src="screenshots/14_Budget_Alert_Configured_png.jpg" alt="Budget Alerts" />
      <p align="center"><em>Budget monitoring (50%/75%/90%)</em></p>
    </td>
  </tr>
</table>

### Network Topology & Testing

<table>
  <tr>
    <td width="50%">
      <img src="screenshots/15_Network_Topology_Diagram_png.jpg" alt="Topology View" />
      <p align="center"><em>Azure Network Watcher topology</em></p>
    </td>
    <td width="50%">
      <img src="screenshots/17_Storage_Connection_Test_png.jpg" alt="Connectivity Test" />
      <p align="center"><em>PowerShell connectivity verification</em></p>
    </td>
  </tr>
</table>

## Skills Demonstrated

### Cloud Infrastructure
- Azure resource organization (resource groups, tagging, naming conventions)
- Virtual network design and subnet segmentation
- Network security group configuration and firewall rule management
- Public/private IP address allocation

### Compute Services
- Virtual machine deployment (Windows Server 2022)
- VM sizing selection based on workload requirements
- Remote desktop protocol (RDP) configuration
- Auto-shutdown scheduling for cost optimization

### Storage Management
- Azure Storage account creation and configuration
- Blob storage container management
- Data upload and access tier selection
- Soft delete configuration for data protection

### Backup & Disaster Recovery
- Recovery Services Vault setup
- VM backup policy configuration
- Manual backup triggering and monitoring
- Retention policy management

### Cost Control
- Budget creation with multi-threshold alerts
- Cost analysis by resource and service
- Resource deallocation strategies
- Spending forecast monitoring

### Network Monitoring
- Azure Network Watcher topology visualization
- Network connectivity testing (PowerShell Test-NetConnection)
- Infrastructure diagram generation and documentation

### Security
- Least-privilege access control (IP-restricted NSG rules)
- Secure remote access configuration
- Private network isolation
- Encryption at rest (managed keys)

## Tools & Technologies

- **Cloud Platform:** Microsoft Azure
- **Compute:** Azure Virtual Machines, Windows Server 2022
- **Networking:** Azure Virtual Network, Network Security Groups, Azure Network Watcher
- **Storage:** Azure Blob Storage, LRS redundancy
- **Backup:** Azure Backup, Recovery Services Vault
- **Monitoring:** Azure Monitor, Cost Management
- **Remote Access:** Windows Remote Desktop Protocol (RDP)
- **Testing:** PowerShell (Test-NetConnection)

## Project Structure

```
Azure-Cloud-Infrastructure-Lab/
│
├── README.md                          # Project documentation
├── network-topology.svg               # Network architecture diagram
│
└── screenshots/                       # Visual documentation
    ├── 01_Resource_Group_Created_png.jpg
    ├── 02_Virtual_Network_Created_png.jpg
    ├── 03_NSG_With_RDP_Rule_png.jpg
    ├── 04_VM_Deployment_Complete_png.jpg
    ├── 05_VM_Overview_Running_png.jpg
    ├── 06_RDP_Connected_To_VM_png.jpg
    ├── 07_Storage_Account_Created_png.jpg
    ├── 08_Blob_Container_Created_png.jpg
    ├── 09_Blob_File_Uploaded_png.jpg
    ├── 10_Recovery_Vault_Created_png.jpg
    ├── 11_VM_Backup_Configured_png.jpg
    ├── 12_Backup_Job_In_Progress_png.jpg
    ├── 13_Cost_Analysis_View_png.jpg
    ├── 14_Budget_Alert_Configured_png.jpg
    ├── 15_Network_Topology_Diagram_png.jpg
    ├── 16_Topology_SVG_Downloaded_png.jpg
    └── 17_Storage_Connection_Test_png.jpg
```

## Key Takeaways

1. **Network Security First:** The NSG configuration demonstrates defense-in-depth by restricting access to only necessary protocols and source IPs.

2. **Cost-Conscious Design:** VM deallocation during off-hours reduces monthly costs by approximately $40 while maintaining infrastructure availability for development/testing.

3. **Backup is Non-Negotiable:** Automated daily backups with 30-day retention provide protection against data loss without manual intervention.

4. **Documentation Matters:** Network topology visualization (via Azure Network Watcher) provides instant clarity on resource relationships and simplifies troubleshooting.

5. **Start Small, Plan Big:** The /16 address space with /24 subnets allows for future expansion without rearchitecting the foundation.

## Related Projects

- **[PowerShell IT Administration Automation](https://github.com/lionelmsango/PowerShell-IT-Admin-Automation)** - Active Directory user lifecycle management scripts
- **Microsoft 365 Administration Lab** - Entra ID, Exchange, and SharePoint configuration
- **SIEM Deployment (Wazuh)** - Security monitoring for Windows/Linux endpoints

## About

This project was developed as part of a comprehensive IT administration portfolio demonstrating cloud infrastructure skills for enterprise environments. The infrastructure follows Azure Well-Architected Framework principles for security, reliability, and cost optimization.

**Author:** Lionel Sango  
**Certification:** Microsoft Certified: Azure Fundamentals (AZ-900)  
**LinkedIn:** [linkedin.com/in/lionel-sango-417681273](https://www.linkedin.com/in/lionel-sango-417681273/)

---

**Note:** This infrastructure was deployed for learning and demonstration purposes. All resources were properly deallocated after documentation to minimize costs. Total project cost: <€1.
