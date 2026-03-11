# Create UAL Auditing Alert Policy in Microsoft Purview (US Gov)

Deploys a Microsoft Purview alert policy that fires whenever `Set-AdminAuditLogConfig` is executed — the cmdlet used to enable or disable Unified Audit Log (UAL) ingestion. Supports both **Commercial** and **GCC High (GCCH)** tenants.

---

## 📋 Prerequisites

- **ExchangeOnlineManagement** module v3+
- Admin account with **Manage Alerts** or **Organization Configuration** role in Microsoft Purview

---

### ✅ Step 1: Install & Import the Exchange Online Module

```powershell
Install-Module ExchangeOnlineManagement -Scope CurrentUser -Force
Import-Module ExchangeOnlineManagement
```

---

### ⚙️ Step 2: Configure Script Variables

Before running, edit the following values at the top of `Create-UAL-Alert.ps1`:

```powershell
# Set to $true for GCC High, $false for Commercial
$IsGCCH = $true

# UPN of the admin account used to connect
$AdminUPN = "admin@yourtenant.onmicrosoft.us"

# Email address(es) to receive alert notifications
$NotifyUser = "admin@yourtenant.onmicrosoft.us"
```

---

### 🔐 Step 3: Connect to Security & Compliance PowerShell

The script connects automatically based on your `$IsGCCH` setting.

**GCC High (GCCH):**
```powershell
Connect-IPPSSession `
    -UserPrincipalName $AdminUPN `
    -ConnectionUri "https://ps.compliance.protection.office365.us/powershell-liveid/"
```

**Commercial:**
```powershell
Connect-IPPSSession -UserPrincipalName $AdminUPN
```

---

### 🔍 Step 4: Run the Script

```powershell
.\Create-UAL-Alert.ps1
```

The script will:
1. Check if the alert policy already exists (skips creation if found)
2. Create a **High severity** alert policy scoped to `Set-AdminAuditLogConfig`
3. Verify and display the policy details upon completion

---

### ✅ Step 5: Verify the Alert Policy Was Created

```powershell
Get-ProtectionAlert | Where-Object { $_.Name -eq "UAL Auditing Configuration Changed - CRITICAL" } | Format-List Name, Operation, Severity, IsEnabled, NotifyUser
```

#### Expected output should show:

```powershell
Name       : UAL Auditing Configuration Changed - CRITICAL
Operation  : Set-AdminAuditLogConfig
Severity   : High
IsEnabled  : True
NotifyUser : admin@yourtenant.onmicrosoft.us
```

---

### 🔎 Step 6: Investigate a Triggered Alert

If the alert fires, search the UAL to confirm whether auditing was disabled:

```powershell
Search-UnifiedAuditLog `
    -Operations Set-AdminAuditLogConfig `
    -StartDate (Get-Date).AddDays(-7) `
    -EndDate (Get-Date)
```

> Check the `AuditData.UnifiedAuditLogIngestionEnabled` field in the results to confirm if UAL was turned **off**.

---

### 🔌 Disconnect When Done

```powershell
Disconnect-ExchangeOnline -Confirm:$false
```

---

## 📌 Notes

- It can take **up to 24 hours** for a new policy to sync to the detection engine.
- This alert fires on **any** execution of `Set-AdminAuditLogConfig` — whether UAL is being enabled **or** disabled. Always investigate to confirm intent.
- Verify and manage the policy in the Defender portal:
  - **GCCH:** https://security.microsoft.us/alertpoliciesv2
