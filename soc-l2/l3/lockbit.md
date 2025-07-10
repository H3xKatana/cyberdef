

### 🛡️ Incident Summary: Ransomware Attack on Medium-Sized Corporation

A medium-sized corporation experienced a ransomware attack, first identified when a user reported a **ransom note** on their screen, alongside a **Windows Defender alert** indicating malicious activity.

Your task is to analyze logs from compromised machines and determine the ransomware's **entry point and behavior**.

---

### 🔍 **DC01 (Domain Controller)**

* **Flagged Executable**:

  * Reviewed **Windows Defender Operational** log.
  * **Event IDs 1116 and 1117** confirmed flagged files.
  * **Exclusion Path Found**: `C:\` was excluded, likely by the attacker to avoid detection.

* **PowerShell Activity**:

  * Found suspicious `Set-MpPreference` usage to **disable real-time protection**:

    ```powershell
    Set-MpPreference -DisableRealtimeMonitoring 1
    ```

* **Suspicious IP Activity**:

  * **Sysmon Event ID 3** shows connection to:

    ```
    rundll32.exe → 192.168.170.142
    ```

---

### 🗄️ **SQL Server**

* **Flagged by Windows Defender** (same Event IDs: 1116/1117)

* Detected suspicious process:

  * `cmd.exe`
  * **Parent process**: `sqlservr.exe`

* **Brute-force Login Attempt**:

  * SQL Server logs show brute-force attempts on **user: SA**

* **Configuration Modification**:

  * `xp_cmdshell` was **enabled**, allowing command execution.

* **PowerShell Execution**:

  * After AV was disabled, a script was executed:

    ```
    fJSYAso.ps1
    ```

* **Process Injection**:

  * Detected with Sysmon Event ID **596**

* **Scheduled Task Created**:

  * Task name: `UpdateCheck`

* **Malicious Dump Process**:

  * `rundll32.exe` with **PID 5456**

* **Remote Command to FileServer**:

  * PowerShell used with base64-encoded payload to disable Defender remotely:

    ```powershell
    Invoke-Command -ComputerName FileServer -ScriptBlock {
        reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
    }
    ```

---

### 📁 **FileServer**

* **Flagged Binary**:

  * `ceabe99.exe` detected and flagged by Windows Defender

---

### 💻 **DevPC**

* **Sysmon Logs**:

  * A suspicious file was created in the temp directory:

    ```
    vmware.exe
    ```

---

### 📄 **Ransom Note Discovered**

* Found dropped ransom note:

  ```
  C:\Users\dmiller\Downloads\HHuYRxB06.README.txt
  ```

---

### ✅ **Key Findings Summary**

| Component        | Indicator                                            |
| ---------------- | ---------------------------------------------------- |
| Entry Point      | Likely via SQL brute-force → `xp_cmdshell`           |
| Initial Script   | `fJSYAso.ps1` (executed after disabling Defender)    |
| AV Disabled      | `Set-MpPreference -DisableRealtimeMonitoring`        |
| Malicious Binary | `ceabe99.exe`, `vmware.exe`                          |
| Lateral Movement | PowerShell + `Invoke-Command` to FileServer          |
| Persistence      | Task `UpdateCheck` + Defender registry modifications |
| Ransom Note      | `HHuYRxB06.README.txt` in dmiller's Downloads folder |
