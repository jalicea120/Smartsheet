## ✅ Enable Unified Audit Log Ingestion in PowerShell (US Gov)

To enable Unified Audit Log Ingestion for Microsoft 365, follow the steps below.

### ✅ Step 1: Install & Import Exchange Online Module (US Gov ready)

``` powershell
Install-Module ExchangeOnlineManagement
Import-Module ExchangeOnlineManagement 
```

### 🔐 Step 2: Connect to Exchange Online (US Gov)
``` powershell
Connect-ExchangeOnline -ExchangeEnvironmentName O365USGovGCCHigh
```

### 🔍 Step 3: Check current audit log ingestion status
``` powershell
Get-AdminAuditLogConfig | Format-List UnifiedAuditLogIngestionEnabled
```

### ⚙️ Step 4: (If Disabled = False) Enable Unified Audit Log Ingestion
``` powershell
Set-AdminAuditLogConfig -UnifiedAuditLogIngestionEnabled $true
```

### 🔄 Step 5: Confirm the change after waiting (may take 30–60 min)
```powershell
Get-AdminAuditLogConfig | Format-List UnifiedAuditLogIngestionEnabled
```

#### Final value should show as:

``` powershell
UnifiedAuditLogIngestionEnabled : True
```

### ✅ Disconnect when done
``` powershell
Disconnect-ExchangeOnline
```
