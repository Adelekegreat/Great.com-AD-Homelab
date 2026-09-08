# Great.com: A Windows Server & Active Directory Infrastructure Lab

## Project Overview
This project documents the design and configuration of an on-premises Windows Server enterprise environment. The lab focuses on core identity and user management, high-availability network services, and security best practices using Active Directory Domain Services (AD DS), DNS, DHCP, and Group Policy.

## Infrastructure Architecture
* **Domain Controllers:** 2 ( Primary DC [Great-DC-01] and Secondary DC [Great-DC-02] )

    <img width="550" height="231" alt="domain controllers" src="https://github.com/user-attachments/assets/3573ba1b-2ee4-466e-9ab7-9f3c081572a5" />

<br><br>

* **DHCP Servers:** 1 Primary [Great-DHCP], with a Hot Standby configured on DC-2

    <img width="501" height="260" alt="dhcp" src="https://github.com/user-attachments/assets/a75979c5-024c-4711-84a9-cce94fd6f6b5" />

<br><br>

* **Hypervisor Network:** VMware native DHCP disabled to allow the Windows DHCP server to manage IP distribution.

    <img width="449" height="375" alt="image" src="https://github.com/user-attachments/assets/ace22f8e-3489-4592-81a2-d957bcec6813" />
    
<br><br>

* **Clients pc:** 2 (Gr8laptop01 and Gr8laptop02)
    
    <img width="614" height="256" alt="computers" src="https://github.com/user-attachments/assets/8a45bdc8-f199-4543-a925-6c85da04e5dc" />

<br><br>

## Infrastructure Architecture Diagram

   <img width="900" height="500" alt="image" src="https://github.com/user-attachments/assets/b80ffa43-87e6-4bb5-bcfd-8c1f7a523262" />

<br><br>   

## Key Implementations & Configurations

### 1. Network Services (DNS & DHCP)
* **DHCP Scope Configuration:**  I Configured the primary DHCP server to distribute IP addresses to clients, with specific exclusions for the static IPs of the Domain Controllers and the DHCP server itself. I disabled VMware Workstation's native NAT DHCP service first, otherwise it competes with the domain's DHCP server for the same address space.

  <img width="436" height="181" alt="image" src="https://github.com/user-attachments/assets/281b57cb-7afa-4019-ba18-d6750244686d" />

<br><br>

* **High Availability DHCP:** Configured a DHCP failover relationship using **Hot Standby** mode on DC-2 to ensure network resilience. One thing that tripped me up was my reservations didn't replicate to the hot-standby the way the rest of the scope config did. Had to force it manually.

    <img width="419" height="356" alt="new failover" src="https://github.com/user-attachments/assets/24ad2972-975a-436a-8221-a4bec39a2464" />
    <img width="419" height="356" alt="scope final  hotstanby" src="https://github.com/user-attachments/assets/22b900c4-d68f-40d1-9d25-e912ff847df9" />

<br><br>

* **IP Reservations:** Created a specific IP reservation for client `gr8laptop01`.

    <img width="419" height="356" alt="Screenshot 2026-09-06 073525" src="https://github.com/user-attachments/assets/c8493739-6089-47f2-83a3-b122a08cb7f2" />

<br><br>
     
* **Scope Backup:** Successfully performed a backup of the DHCP scope for disaster recovery purposes.

    <img width="419" height="356" alt="dhcp backup" src="https://github.com/user-attachments/assets/a5acd679-d2e0-44f1-805d-def64f125cda" />

<br><br>

* **DNS:** Configured a Reverse Lookup Zone for proper IP-to-hostname and PTR resolution.

    <img width="419" height="356" alt="Screenshot 2026-09-06 083158" src="https://github.com/user-attachments/assets/a623ba06-55f5-4c98-834d-e74a27a09ccd" />
   
<br><br>

### 2. Active Directory & Identity Management
* **Administrative Accounts:** Created the primary Domain Admin account (`Great`) to handle day-to-day administration instead of the built-in `Administrator` account, for accountability and audits.

    <img width="419" height="356" alt="Screenshot 2026-09-07 121200" src="https://github.com/user-attachments/assets/ed0353a6-df55-4562-95b0-3af08673f0d8" />

<br><br>

* **OUs and Groups:** After Creating the Admin account, i created multiple OU and security groups to organize users, Computers and other objects and also for group policy application purpose. The security groups were created to assign permissions and access.

  <img width="419" height="356" alt="Screenshot 2026-09-07 122526" src="https://github.com/user-attachments/assets/1820c77a-648f-45fe-a123-542a6f909460" />

<br><br>

* **User Provisioning:** Created Structured standard user accounts (e.g., `Tim Hamm`) and organized them into appropriate OUs.

   <img width="419" height="356" alt="Screenshot 2026-09-08 065226" src="https://github.com/user-attachments/assets/13790dd8-7c3c-46b7-b4db-101f4ee507fe" />

<br><br>

* **Delegation of Control (RBAC):** 
  * Created a dedicated IT User account (`Mary Jane`).
  * Assigned the account to an IT Security Group.
  * Successfully delegated specific administrative controls to this group, ensuring permissions are properly documented and restricted based on the principle of least privilege.

### 3. Group Policy Objects (GPOs)
* **GPO Deployment:** Created custom Group Policy Objects under group policy object at the domain level. Edited them and linked them to specific Organizational Units (OUs) to manage the client environments.

    <img width="419" height="356" alt="image" src="https://github.com/user-attachments/assets/9fcffe3c-f380-4066-8fb9-149b8a02b94f" />

    Result of the GPO Created

    <img width="419" height="356" alt="Screenshot 2026-09-08 074954" src="https://github.com/user-attachments/assets/a14ac0f3-eeda-47f7-981d-61a25760504e" />

    <br><br>

* **Security Group Mapping:** Ensured all necessary users and security groups were fully established before GPO creation to ensure policies applied correctly to the target audiences.


### 4. Security & Password Policies
* **Baseline Security:** Edited the `Default Domain Policy` to establish a baseline password policy for standard users.

    <img width="419" height="356" alt="Screenshot 2026-09-08 071257" src="https://github.com/user-attachments/assets/8306be65-a008-47a7-b570-bbd1e9dd3a65" />

<br><br>

* **Break-Glass Administration Account:** 
  * I Created a highly secure emergency access account. used only if normal admin access is unavailable. 
  * I Restricted the account's logon rights strictly to Domain Controllers. Did this incase if a user comes across the login credentials , its useless to them.

    <img width="419" height="356" alt="Screenshot 2026-09-08 093854" src="https://github.com/user-attachments/assets/927922c6-261c-48c6-9709-21133e23a4e4" />
    
    <img width="419" height="356" alt="image" src="https://github.com/user-attachments/assets/3cbfcdab-ce97-43cd-aac1-9ba153703b4a" />

<br><br>
  
  * Configured a Fine-Grained Password Policy (FGPP) specifically for the break-glass security group via the Active Directory Administrative Center (ADAC). Since The lockout policy above applies to this account too by default, an account meant for emergencies shouldn't be the thing that's locked out during one. I Fixed this with a Fine-Grained Password Policy scoped to the `Tier 0 - GlassBreak` group, lockout threshold set to 0. PSOs only target users or groups, not OUs, which is why the group exists. I also set the precedence to 10 , but it doesn't really matter cause it's the only 

    <img width="419" height="356" alt="Screenshot 2026-09-08 145218" src="https://github.com/user-attachments/assets/339c6282-a71f-4449-8b40-9356c1c1824d" />
    <img width="419" height="356" alt="Screenshot 2026-09-08 150022" src="https://github.com/user-attachments/assets/a404a00f-c9e5-483c-ac6a-72baa28b1682" />

<br><br>

* **Incident Simulation:** Successfully simulated an Admin account lockout event and executed the administrative unlock procedure to test helpdesk capabilities.

    <img width="419" height="356" alt="Screenshot 2026-09-08 153915" src="https://github.com/user-attachments/assets/b935a1a2-d64f-4c1e-91b1-68e23217c7bb" />

    **logging in with Glass-break account**

    <img width="419" height="356" alt="Screenshot 2026-09-08 154602" src="https://github.com/user-attachments/assets/285ad0e7-5933-4bc4-ab04-92aa844e96b9" />

    **Unlocking the Account**
    
    <img width="644" height="409" alt="Screenshot 2026-09-08 160947" src="https://github.com/user-attachments/assets/5c84456d-ef77-49d9-bb16-3e5b55e4a7e1" />

## Stack

Windows Server, Active Directory Domain Services, DNS, DHCP, Group Policy Management, Active Directory Administrative Center, VMware Workstation.

---
Author: *Great Adeleke /Adelekegreat*
