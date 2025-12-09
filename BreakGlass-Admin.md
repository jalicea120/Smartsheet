## ✅ Enable Unified Audit Log Ingestion in PowerShell (US Gov)

To enable Unified Audit Log Ingestion for Microsoft 365, follow the steps below.

### ✅ Step 1: Under Advance Hunting, paste the following KQL query after updating the *breakglass-admin@<TENANT_NAME>.onmicrosoft.us* with the client's information.

``` powershell
IdentityLogonEvents
| where AccountUpn =~ "breakglass-admin@<TENANT_NAME>.onmicrosoft.us"
| where ActionType in ("LogonSuccess", "LogonFailed")
| project
    Timestamp,
    AccountUpn,
    ActionType,
    FailureReason,
    IPAddress,
    Location,
    Application,
    DeviceName,
    LogonType,
    ReportId 
```

### 🔐 Step 2: On the top right of the Query Builder, click on "Create detection rule"
<img width="1482" height="585" alt="CustomDR" src="https://github.com/user-attachments/assets/a115a4a0-cc92-4612-b148-adb7f8afd2e6" />

### 🔐 Step 3: Enter the following values on the fields 

### General
1. **Detection name**: Breakglass SignIn Alert
1. **Rule Description**: Alert when the breakglass admin account signs in successfully, or fails to sign in.
1. **Frequency**: Continuous (NRT)
1. **Severity**: High
1. **Category**: Suspicious activity
1. **Thread analytics report**: (Leave blank)
1. **Recommended actions**: Verify legitimacy of login, and review IP address. Get clarification from SOC manager.

### Alert settings
1. **Alert title**: BreakGlass Account login alert
1. **Description**: 

> A sign-in was detected using the breakglass account ({{AccountUpn}}) at {{Timestamp}}.
Only use this account for emergency access. Confirm with the security officer whether the login is authorized.

- Under **Entity mapping**, use the following values:
<img width="686" height="517" alt="image" src="https://github.com/user-attachments/assets/1ccb2aa0-f526-42bf-8e8c-65001e699d37" />

### Automated actions (Skip)
### Review and create (Click Submit)
