# Group Policy: Account Lockout and Security Management in Azure AD

## Overview

This repository demonstrates the configuration and management of account lockout policies within Azure Active Directory (AD) using Group Policy. It covers setting lockout thresholds, durations, and reset counters, as well as enabling/disabling accounts, and observing relevant security logs. This is a practical exercise in enhancing security posture by preventing brute-force attacks and managing user access effectively.

## Prerequisites / Technologies Used

* Microsoft Azure:
    * Azure Virtual Machines (Windows Server 2022 - DC-1, Windows 10 - Client-1)
    * Azure Active Directory Domain Services (AD DS)
* Windows Server 2022:
    * Group Policy Management Console (GPMC)
    * Active Directory Users and Computers (ADUC)
    * Event Viewer
* Windows 10:
    * Event Viewer
* Remote Desktop Protocol (RDP)
* Pre-existing Domain Controller (DC-1) with a configured domain and admin and user accounts.

## Steps

### 1. Account Lockout GP Configuration and Simulation

* Establish an RDP connection to DC-1 (Domain Controller) as a Domain Admin
* On DC-1, pen Group Policy Management Console (GPMC), Start -> Run -> `gpmc.msc`.
![image](https://github.com/user-attachments/assets/a5f246cf-de34-4c3b-b790-d1f8e237f377)
* Navigate to the domain forest -> Domains -> (domain name) -> Group Policy Objects
* ![image](https://github.com/user-attachments/assets/6853b190-c27e-4a0c-84fe-34048a0df5b9)
* Can right click and edit the existing `Default Domain Policy` 
* Go to `Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy`.
* ![image](https://github.com/user-attachments/assets/b43d3351-5b5f-4d6e-a2e8-3b2a6e628229)
* Configure Account Lockout Policy Settings by double clicking each policy, check `Define this policy setting` and:
    * **Account Lockout Duration:** Set the duration (e.g., 30 minutes).
    * **Account Lockout Threshold:** Set the threshold (e.g., 5 invalid attempts).
    * **Allow Administrator Account Lockout:** Enabled
    * **Reset Account Lockout Counter After:** Set the reset time (e.g., 15 minutes).
* ![image](https://github.com/user-attachments/assets/6ed574bb-4c52-4d99-8304-1d3ef9739298)
* ![image](https://github.com/user-attachments/assets/8de911b7-d957-4b4c-9b80-8d09f957585b)
* Go to the `Default Domain Policy` settings and view the new changes
* ![image](https://github.com/user-attachments/assets/ada43fb4-d75d-4c42-a473-eaeebfa27e3e)
* This policy is already linked to each Organizational Unit as its the default policy
* ![image](https://github.com/user-attachments/assets/ad86e2ac-8a6e-45c2-80a4-b913927ad4b7)
* Updating the Group Policy takes 90 minutes but can be forced:
    * On a client machine or DC-1 as a Domain Admin, run `gpupdate /force` in Command Prompt or Powershell.
    * ![image](https://github.com/user-attachments/assets/5f304448-a8d9-499d-ada4-45b063cac737)
* Select a random Domain User Account in ADUC to test the policy
  * Open RDP to Client-1 and enter the Username 
  * Attempt to log in with the test account 5 times with an incorrect password.
  * ![image](https://github.com/user-attachments/assets/4930e03d-a14f-4a07-ac37-176fc9e56b56)
* Observe Account Lockout and Unlock:
    * In the Domin Controller open ADUC
    * Right click domain name and click `Find` and enter User you just locked out
    * ![image](https://github.com/user-attachments/assets/2d5aa02c-39e9-4cee-aa89-e7abe8dead15)
    * Verify the account is locked in ADUC by double clicking the User to open its Properties and go to Account
    * ![image](https://github.com/user-attachments/assets/8c418208-5457-4a0d-8602-6037e21d88ad)
    * Note the locked message
    * If you want you can check the box to have User set new password
    * ![image](https://github.com/user-attachments/assets/9b602b1f-987c-457b-a965-e6ac9f1f4f7d)
    * Check `Unlock Account` and click Apply and Ok
* Attempt Login:
    * Attempt to log in again as that User on Client-1
 
### 2 Resetting Passwords

* Go back to DC-1 as Admin
* Select a User in ADUC
* Right click user and click `Reset Password`
* Enter new password and if needed check unlock the account
* ![image](https://github.com/user-attachments/assets/601a0121-963f-4b63-a89b-f0c1c69d5574)
* ![image](https://github.com/user-attachments/assets/6e8b0e0b-042f-4d75-a68b-4516dce09101)


### 3. Enabling and Disabling Accounts

* Disable Account:
    * Open ADUC and pick any user
    * Right click and click `Disable Account`
    * ![image](https://github.com/user-attachments/assets/3bd7f923-8b78-46c7-bf47-73b1cf6c30de)
    * ![image](https://github.com/user-attachments/assets/0d3024b9-425b-4db1-8ac5-adc878d2bfac)
* Attempt Login:
    * Attempt to log in with the disabled account and observe the error.
    * ![image](https://github.com/user-attachments/assets/12c490b0-f4a2-4a20-a99e-20ad4c1653a7)
* Re-enable Account:
    * Right click the User in ADUC and click `Enable Account`
* Attempt Login:
    * Attempt to log in with the enabled account.

### 4. Observing Logs

* Domain Controller Logs:
    * Open Event Viewer on DC-1 (run `eventvwr.msc` or search Event Viewer).
    * Navigate to `Windows Logs > Security`.
    * Right click Security and click Find
    * Observe events with ID 4740 (Account Lockout).
    * ![image](https://github.com/user-attachments/assets/d2662285-0dd7-461e-a6af-4542cef08d53)
* Client Machine Logs:
    * Open Event Viewer on Client-1 (as an Admin)
    * If logged into Client-1 as a Domain User, Open EV as an admin and enter Domain Admin Credentials
    * Navigate to `Windows Logs > Security`.
    * ![image](https://github.com/user-attachments/assets/caa1de22-52cf-4195-a453-5886d4247f9d)
    * Observe events with ID 4625(failed login) or 4624 (success).

## Takeaways

* Successfully configured account lockout policies using Group Policy.
* Demonstrated the impact of lockout policies on user access.
* Learned to unlock accounts and reset passwords in ADUC.
* Practiced enabling and disabling user accounts for access control.
* Gained experience in interpreting security logs on both the Domain Controller and client machines.
* Understood the importance of account lockout policies in preventing brute-force attacks.
* Recognized the role of log monitoring in security incident detection and response.
* Reinforced the importance of Group Policy for managing security settings in Active Directory.
