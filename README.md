# OmniCorp Identity Governance & Administration (IGA) Project

## Project Overview

This project focuses on Identity Governance & Administration (IGA) and identity lifecycle management using Microsoft Entra ID.

The project is based on the OmniCorp scenario, where an external compliance audit identified dormant contractor accounts, permission accumulation when employees changed departments, and orphaned administrator accounts belonging to former employees.

The goal is to design a secure Joiner-Mover-Leaver (JML) lifecycle process, perform an access certification review, and implement identity lifecycle controls using Microsoft Entra ID.

## Part 1 — Joiner-Mover-Leaver (JML) Workflow

### Joiner — Onboarding

**Trigger Source:**  
A new employee is entered into Workday by HR.

**Action Taken:**  
The identity provider automatically provisions the employee's corporate identity, assigns appropriate group memberships based on their department, and requires MFA enrollment.

**System of Record:**  
Workday (HR system).

### Mover — Internal Transfer

**Trigger Source:**  
HR updates the employee's department or role in Workday.

**Action Taken:**  
The identity provider removes permissions associated with the employee's previous role and provisions access required for the new role. This prevents permission accumulation and privilege creep.

**System of Record:**  
Workday (HR system).

### Leaver — Offboarding

**Trigger Source:**  
HR records an employee termination or contractor end date.

**Action Taken:**  
The identity provider disables the user's account, removes access to connected applications, and invalidates active sessions to prevent further authentication.

**System of Record:**  
Workday (HR system).
## Part 2 — Access Certification & Audit Campaign

An access review was performed on four high-risk OmniCorp user profiles to identify excessive, dormant, or inappropriate access.

| User | Risk Identified | Action | Justification |
|---|---|---|---|
| j.doe@omnicorp.com | Marketing Manager has Prod-Database-Admin access, which is excessive for the role. | Modify | Remove Prod-Database-Admin while retaining legitimate Marketing and Jira access. |
| c.smith@contractor.io | External contractor has not logged in for 145 days but still has GitHub-Core and AWS-Dev-Access. | Revoke | The account is dormant beyond 120 days and continued access creates unnecessary security risk. |
| a.jones@omnicorp.com | Former SysAdmin terminated 180 days ago still has Global-Tenant-Admin and Domain-Controllers access. | Revoke | A terminated employee must not retain privileged administrative access. |
| m.chen@omnicorp.com | Employee transferred from Sales to Finance but retains Sales-CRM-Full access alongside Finance access. | Modify | Remove the old Sales permissions and retain only the Finance access required for the new role. |

### Access Review Outcome

The review identified three major Identity Governance risks:

- **Excessive Privileges:** Access that is not required for the user's current job role.
- **Dormant and Orphaned Accounts:** Accounts remaining active after long periods of inactivity or termination.
- **Permission Creep:** Employees retaining old permissions after changing roles or departments.

The appropriate remediation is to certify only legitimate access, modify excessive permissions, and revoke access that is no longer required.
## Part 3 — Microsoft Entra ID Hands-On Implementation

### Step 1 — Create a Lifecycle Security Group

Created a Microsoft Entra ID security group named:

`SG-Temporary-Contractors`

The group was configured as a Security group with Assigned membership. This demonstrates group-based identity lifecycle management instead of assigning permissions individually to each user.
![Lifecycle Security Group](01-security-group-created.png)
### Step 2 — Provision a Test Contractor (Joiner)

Created a new Microsoft Entra ID user:

`Test Contractor`

The user represents a new external contractor joining OmniCorp.

The contractor was added to:

`SG-Temporary-Contractors`

This demonstrates the Joiner stage of the identity lifecycle and the use of group membership to manage access.
![Test Contractor Group Membership](02-contractor-group-membership.png)
### Step 3 — Create a Test Enterprise Application

Created a non-gallery Enterprise Application named:

`OmniCorp-Contractor-App`

The application was created to simulate a corporate application that contractors would need to access.
![Test Contractor Before Offboarding](03-test-contractor-before-offboarding.png)
### Step 4 — Attempt Group-Based Application Assignment

An attempt was made to assign `SG-Temporary-Contractors` to the `OmniCorp-Contractor-App`.

The Microsoft Entra tenant reported that groups were not available for assignment because of the current Active Directory plan level.

Because of this tenant licensing limitation, direct group assignment to the Enterprise Application could not be completed.

The security group and contractor membership were successfully configured, and the licensing restriction was documented as part of the lab evidence.
![Test Contractor Account Disabled](04-test-contractor-account-disabled.png)
### Step 5 — Execute Contractor Offboarding (Leaver)

To simulate the end of the contractor's lifecycle, the `Test Contractor` account was disabled in Microsoft Entra ID.

The **Account enabled** setting was turned off and the change was saved.

This demonstrates the Leaver / kill-switch process by preventing the former contractor from continuing to authenticate while retaining the identity record for governance and audit purposes.

## Security Outcomes

This project demonstrated:

- Joiner-Mover-Leaver (JML) lifecycle management
- Group-based identity administration
- Access certification and remediation
- Prevention of permission creep
- Identification of dormant and orphaned accounts
- Enterprise application access concepts
- Secure contractor offboarding
- Microsoft Entra ID identity governance

## Key Lessons Learned

Identity Governance is not only about granting access. Access must also be reviewed, modified, and revoked throughout the identity lifecycle.

Using groups helps organizations manage access consistently and reduces individual permission assignments.

The Joiner-Mover-Leaver model ensures that users receive appropriate access when they join, have obsolete access removed when their role changes, and lose authentication access promptly when they leave.
