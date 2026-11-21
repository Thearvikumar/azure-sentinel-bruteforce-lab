# Azure Sentinel (SIEM) Brute Force Attack Detection Lab

## Objective
The goal of this project was to deploy a cloud-based honeypot virtual machine in Microsoft Azure, ingest Windows Security Event logs into a Log Analytics Workspace, and configure Microsoft Sentinel to detect and alert on simulated RDP brute-force attacks.

## Tools & Technologies Used
* **Cloud Provider:** Microsoft Azure
* **SIEM:** Microsoft Sentinel
* **Log Management:** Log Analytics Workspace, Azure Monitor Agent, Data Collection Rules
* **Compute & Networking:** Windows Server Virtual Machine, Network Security Groups (NSG)
* **Query Language:** Kusto Query Language (KQL)

## Architecture & Data Flow
1. **Target:** A Windows Server VM was deployed as a honeypot, with its Network Security Group modified to expose RDP (Port 3389) to the internet.
2. **Log Collection:** An Azure Data Collection Rule was configured to push Windows Security Event logs (specifically failed logins) to a Log Analytics Workspace.
3. **Detection:** A custom KQL query was written in Microsoft Sentinel to detect when an attacker generated 5 or more failed login attempts (Event ID 4625) within a 1-hour window.
4. **Alerting:** Sentinel generated a High-severity incident, mapping the attacker's IP, target account, and host machine for SOC analyst investigation.

## The Detection Query (KQL)
This query identifies multiple failed login attempts originating from the same IP address:

 SecurityEvent
 | where EventID == 4625
 | where LogonType in (3, 10)
 | summarize FailedLogonCount = count() by TargetAccount, IpAddress, Computer
 | where FailedLogonCount >= 5

## Visual Evidence

**1. Incident Triggered in Sentinel Dashboard**

<img width="1202" height="632" alt="sentinel_incident_queue" src="https://github.com/user-attachments/assets/a1576081-a186-4965-a885-f1a4f944c379" />
<img width="1212" height="653" alt="sentinel_incident_metrics" src="https://github.com/user-attachments/assets/2348c2fe-fcd6-441b-9207-7c0ec7b07716" />


**2. Incident Investigation Graph with Entity Mapping**

<img width="1204" height="649" alt="incident_investigation_graph" src="https://github.com/user-attachments/assets/a7982f3a-9419-4b96-b907-da7fc73e1b09" />

## Conclusion
This project was undertaken to successfully demonstrate the end-to-end configuration of a cloud-based SIEM pipeline. It demonstrates all applications from infrastructure provisioning and log ingestion to custom rule creation and entity mapping.
