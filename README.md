# https://github.com/edwardchibuzo/Architecture_Diagram.png/blob/main/architecture-diagram%20(2).png
Architecture diagram for Bundle EHR.

## 🏗️ System Authorization Boundary — Architecture Diagram

[![Architecture Diagram](diagrams/Architecture_Diagram.png)](diagrams/Architecture_Diagram.png)

This diagram illustrates the full system authorization boundary for **Bundle EHR**, 
a hybrid Electronic Health Record Management System operating across three distinct 
trust zones — each with clearly defined security responsibilities.

**🟠 Internet / Untrusted Zone**
All external traffic — whether from internet users or patient mobile devices — enters 
the system exclusively through HTTPS, immediately intercepted by AWS WAF before 
any application layer is reached. No untrusted traffic ever touches Bundle's core 
infrastructure directly.

**🔵 AWS IaaS Zone (Shared Responsibility)**
Filtered traffic passes through an Application Load Balancer (ALB) before reaching 
the Professional Web App Server — the brain of the Bundle system. This tier hosts 
all application logic and serves as the central hub for data routing. Snapshots and 
archives are automatically offloaded to **S3_BACKUP** (Amazon S3 + AWS Backup), 
ensuring encrypted, durable backups outside the primary compute layer. AWS manages 
physical infrastructure security; Bundle owns everything above the hypervisor.

**🟢 On-Premises Zone (Org-Controlled)**
The most sensitive assets — the **on-prem database (PHI/PII)** and the **Audit Log 
server** — live entirely within the organisation's physical control, connected to 
the AWS layer exclusively via **IPSec VPN tunnels** for encrypted bi-directional 
communication. Admin Workstations and Clinician Endpoints access the system through 
hardened Management/SSH channels, with all activity captured in the audit log for 
compliance and incident response.

**⚙️ AWS Physical Infrastructure (Outside Authorization Boundary)**
Managed Physical Data Centers sit intentionally *outside* the authorization boundary, 
reflecting the AWS Shared Responsibility Model — AWS secures the physical layer; 
Bundle is accountable for everything running on top of it.

> This architecture enforces a **defence-in-depth** posture aligned with 
> **NIST SP 800-53 Rev 5** controls, **HIPAA Security Rule** requirements, 
> and **FIPS 199 High** impact classification — ensuring confidentiality, 
> integrity, and availability of all Protected Health Information (PHI) 
> and Personally Identifiable Information (PII) handled by Bundle.
