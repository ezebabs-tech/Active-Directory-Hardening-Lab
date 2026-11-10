# Enterprise Active Directory Hardening & Identity Architecture (`corp.local`)

## 1. Executive Summary
- **Project Scope:** Enterprise-grade Active Directory security baseline, domain object hierarchy design, and group policy hardening.
- **Target Domain:** `corp.local` (Domain Controller: `DC01`)
- **Key Deliverables:** Automated PowerShell OU/Group/User deployment script (`setup-ad.ps1`), GPO-based privilege restriction, legacy protocol mitigation, and Advanced Audit Policy verification.

---

## 2. Active Directory Infrastructure & OU Hierarchy
The domain object architecture was deployed using an isolated organizational unit structure (`Corp-Objects`) to enforce strict policy targeting and administrative segregation.

text
corp.local
└── Corp-Objects
├── Administration
├── Groups
├── Servers
├── Users
└── Workstations

### Infrastructure Evidence
![](01-ad-structure-corp-objects.png)

---

## 3. Implemented Hardening & Security Policies

### A. Account & Password Policy (Default Domain Policy)
- **Minimum Password Length:** 14 Characters
- **Password Complexity:** Enforced (Upper/Lowercase, Numbers, Symbols)
- **Account Lockout Threshold:** 5 Invalid Attempts
- **Lockout Duration & Reset Window:** 30 Minutes

*(Paste Screenshot 2 below this line)*

---

### B. Workstation Hardening Policy (`GPO_Workstation_Hardening`)
- **Restricted Groups (Tiered Access Control):** Enforced local `Administrators` group membership, explicitly adding `CORP\SG-Helpdesk-Admins` while automatically revoking unauthorized local administrative privileges across domain workstations.
- **LLMNR Protocol Mitigation:** Disabled Link-Local Multicast Name Resolution via Group Policy (*Turn off multicast name resolution*) under `Computer Configuration -> Administrative Templates -> Network -> DNS Client` to eliminate LLMNR/NBT-NS poisoning and network credential harvesting.

*(Paste Screenshot 3 below this line)*

---

### C. Advanced Event Auditing (`GPO_Audit_Policy`)
Configured granular security auditing subcategories to log identity threats and access attempts:
- **Logon/Logoff:** Audit Logon (**Success and Failure**) | Audit Account Lockout (**Success**)
- **Account Management:** Audit User Account Management (**Success and Failure**)
- **Object Access:** Audit Detailed File Share (**Success and Failure**)

---

## 4. Security Telemetry & Verification Logs

Security event generation was validated on `DC01` by inspecting the Windows Security Log in Event Viewer (`eventvwr.msc`).

| Event ID | Event Description | Forensic Significance | Status |
| :--- | :--- | :--- | :--- |
| **4624** | Successful Account Logon | Captures valid domain authentication events and Logon Types (Type 2 Interactive, Type 3 Network). | **Verified** |
| **4625** | Failed Account Logon | Detects invalid password attempts, brute-force attacks, and credential stuffing activity. | **Verified** |

### Event Verification Evidence
*(Paste Screenshot 4 below this line)*

---

## 5. Repository Structure
text
├── README.md               # Master technical documentation and hardening report
└── setup-ad.ps1            # Master PowerShell provisioning script for OUs, Groups, and Users
