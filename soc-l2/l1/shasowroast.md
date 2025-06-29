
# 🕵️ ShadowRoast Investigation Log

## 🔍 Question

**What’s the malicious file name utilized by the attacker for initial access?**

> ✅ Identified: `C:\Users\sanderson\Downloads\AdobeUpdater.exe`

---

## 📁 Section 1: Detect Executable File Creation (Event ID 11)

```spl
index=shadowroast source="*" event.code=11 winlog.event_data.TargetFilename="*.exe"
| table winlog.event_data.TargetFilename 
| sort _time
```

This query checks for any `.exe` file creation or modification events using **event ID 11** (FileCreate).

---

## 🐾 Section 2: Operations Performed by Malware

```spl
index=shadowroast source="*" winlog.event_data.Image="*AdobeUpdater.exe"
| table winlog.event_data.Image, event.code
| sort _time
```

### Observed Activity:

| Event ID | Description                  |
| -------- | ---------------------------- |
| 1        | Process Creation             |
| 3        | Network Connection           |
| 5        | Thread Injection (possible)  |
| 7        | Image Load                   |
| 11       | File Create                  |
| 13       | Registry Value Set           |
| 29       | DLL Load or handle tampering |

The malware shows a broad range of behaviors, indicating post-exploitation activities.

---

## 💾 Section 3: File Drop Location

The malware appears to interact with:

```
C:\Users\Default\AppData\Local\Temp
```

This may be used as a staging directory for payloads or data exfiltration.

---

## ⚙️ Section 4: Suspicious Activity from Another Executable

```spl
index=shadowroast source="*" winlog.event_data.Image="*BackupUtility.exe"
| table winlog.event_data.Image, event.code
| sort _time
```

Another binary, `BackupUtility.exe`, shows execution — may be part of the attack chain.

---

## 🧾 Section 5: Registry Modifications (Event ID 13)

```spl
index=shadowroast source="*" event.code=13 
| table winlog.event_data.Image, winlog.event_data.TargetObject
| sort _time
```

Used to identify suspicious or malicious registry modifications, often related to persistence or RDP enabling.

---

## 🛠️ Section 6: Command Line Monitoring (Event ID 1)

```spl
index=shadowroast source="*" event.code=1 reg
| table winlog.event_data.CommandLine 
| sort _time
```

Used to trace commands executed by processes that contain `"reg"` — useful for detecting registry tampering.

---

## ✅ Summary

* **Initial Access File:** `AdobeUpdater.exe` executed from `Downloads`
* **Suspicious Actions:** File creation, registry tampering, network activity
* **Potential Persistence:** Temp files and registry entries
* **Tools Used:** Possibly `BackupUtility.exe` as a second-stage payload

