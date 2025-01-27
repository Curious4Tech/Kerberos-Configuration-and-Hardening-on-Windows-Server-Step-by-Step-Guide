# Kerberos Configuration and Hardening on Windows Server

This repository provides a step-by-step guide for configuring and hardening Kerberos authentication on Windows Server. Kerberos is a secure and widely used authentication protocol in Active Directory environments.

## Table of Contents
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Configuration Steps](#configuration-steps)
  - [Step 1: Configure Kerberos Policies](#step-1-configure-kerberos-policies)
  - [Step 2: Enable Strong Encryption](#step-2-enable-strong-encryption)
  - [Step 3: Harden Kerberos Settings](#step-3-harden-kerberos-settings)
  - [Step 4: Synchronize Clocks](#step-4-synchronize-clocks)
- [Validation](#validation)
- [Maintenance and Monitoring](#maintenance-and-monitoring)
- [License](#license)

## Introduction
Kerberos is the default authentication protocol in Active Directory (AD). Properly configuring and hardening Kerberos is essential to:
- Secure authentication processes.
- Prevent attacks like Pass-the-Ticket and Golden Ticket.

This guide will help you configure and secure Kerberos on a Windows Server.

## Prerequisites
- A Windows Server functioning as a Domain Controller (DC).
- Active Directory Domain Services (AD DS) environment.
- Administrator privileges on the DC.

## Configuration Steps

### Step 1: Configure Kerberos Policies
1. Open **Group Policy Management** (`gpmc.msc`).
2. Navigate to **Default Domain Policy** or create a new Group Policy Object (GPO).
3. Modify Kerberos Policy Settings:
   - **Enforce user logon restrictions**: Enabled.
   - **Maximum lifetime for service ticket**: Set to 10 hours or lower.
   - **Maximum lifetime for user ticket**: Set to 10 hours or lower.
   - **Maximum lifetime for user ticket renewal**: Set to 7 days or lower.
   - **Maximum tolerance for computer clock synchronization**: Set to 5 minutes or lower.

### Step 2: Enable Strong Encryption
1. Open **Active Directory Users and Computers** (`dsa.msc`).
2. Navigate to the **Domain Controllers** Organizational Unit (OU).
3. Edit the properties of the Domain Controller account:
   - Go to the **Account** tab.
   - Enable **"This account supports Kerberos AES 256 bit encryption"**.
4. Update the Default Domain Policy:
   - **Network Security: Configure encryption types allowed for Kerberos** → Enable AES128, AES256, and future encryption types.

### Step 3: Harden Kerberos Settings
1. Disable DES encryption:
   - In Group Policy, navigate to **Computer Configuration** → **Windows Settings** → **Security Settings** → **Local Policies** → **Security Options**.
   - Uncheck DES options in **Network Security: Configure encryption types allowed for Kerberos**.
2. Restrict Kerberos delegation:
   - Use **Active Directory Users and Computers**.
   - Set **"Trust this user for delegation to specified services only"** under the **Delegation** tab for each account.
3. Enable auditing for Kerberos events:
   - Open **Local Security Policy** (`secpol.msc`).
   - Navigate to **Advanced Audit Policy Configuration**.
   - Enable:
     - **Audit Kerberos Authentication Service**.
     - **Audit Kerberos Service Ticket Operations**.

### Step 4: Synchronize Clocks
Kerberos relies on time synchronization for ticket validation. Use an NTP server to ensure accurate time:
```bash
w32tm /config /syncfromflags:manual /manualpeerlist:"time.windows.com"
w32tm /resync
```

## Validation
1. Use the `klist` command to verify Kerberos tickets:
   ```bash
   klist
   ```
2. Test by accessing a service (e.g., shared folder or web application) to confirm proper ticket generation and encryption.

## Maintenance and Monitoring
- Review Kerberos event logs in **Event Viewer**:
  - Navigate to **Windows Logs** → **Security**.
- Regularly update security patches and encryption algorithms.

## License
This guide is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
