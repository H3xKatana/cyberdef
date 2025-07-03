
## Visa Checker AWS Breach Analysis – October 15, 2024
## IMDSv1 Lab
On **October 15, 2024**, a major security incident occurred targeting the "Visa Checker" web application, hosted on an **AWS EC2 instance**. The attacker exploited a **Server-Side Request Forgery (SSRF)** vulnerability in the application, which allowed them to access the EC2 instance metadata service at `http://169.254.169.254/latest/meta-data/`. Through this, they successfully **retrieved IAM role credentials**.

Armed with the compromised IAM credentials, the attacker gained access to sensitive data in **Amazon S3**, specifically a bucket containing information on around **20 million tourists**. This breach included extensive data exfiltration. The attacker used **Tor exit nodes** to anonymize their IP addresses, making it difficult to trace the origin of the intrusion.

We were given a **PCAP file** and **CloudWatch logs** to investigate the attack path and determine the scope of the breach.

---

### Breakdown of the Incident

**Q1 – External SSRF Vulnerability**

One of the requests to the vulnerable parameter showed a successful **external redirect** to `www.google.com`, confirming SSRF via redirection. we find this using the pcap file 

**Q2 – Credentials Extraction**

Multiple requests were made to the EC2 metadata endpoint. One of them successfully accessed:

```
http://169.254.169.254/latest/meta-data/iam/security-credentials/EC2-S3-Visa
```

**Q3 – Identity Enumeration**

The attacker used the **`GetCallerIdentity`** API (equivalent to `whoami` for AWS) for initial reconnaissance around:

```
2024-10-15 10:20
```

**Q4 – Attempted EC2 Termination**

An attempt was made to terminate the EC2 instance. Logs show:

```
Client.UnauthorizedOperation
```

indicating the action failed due to insufficient permissions.

**Q5 – Create User Attempt**

Filtering the logs for `CreateUser` events revealed an unauthorized attempt to create a user named:

```
H3ll
```

**Q6 – Attacker CLI Version**

Using the above event, we identified the attacker's AWS CLI version as:

```
AWS-CLI/2.18.5
```

**Q7 – Bucket Discovery**

Log analysis showed the attacker accessed:

* `tourists-visa-info` – the target S3 bucket
* Another bucket used by **CloudWatch** for log storage

**Q8 – Exfiltrated Files and Byte Size**

Using the following `jq` command:

```bash
jq -r '
  .events[]
  | .message
  | fromjson? 
  | select(.eventName == "GetObject")
  | [.requestParameters.key, (.additionalEventData.bytesTransferredOut // 0)]
  | @tsv
' *
```

We extracted all `GetObject` file access events with their respective byte sizes.

We then filtered for files accessed **only once** and summed their total byte size in Python, arriving at:

```
Total bytes for one-time accesses: 55,377,512
```

For the complete dataset (including repeated access), we computed:

```text
1 * 1679309 +  46 * 8388608 +  1 * 1679309 + 62 * 8388608 +
1 * 1679309 + 42 * 8388608 +  1 * 1679309 + 42 * 8388608 +
1 * 1679309 + 38 * 8388608 +  1 * 4194304 + 29 * 8388608 +
1 * 2097152 + 33 * 8388608 +  1 * 2097152 + 25 * 8388608 +
1 * 1679309 + 38 * 8388608 +  1 * 3145728 + 25 * 8388608 +
1 * 4194304 + 36 * 8388608 +  1 * 6291456 + 26 * 8388608 +
1 * 5242880 + 31 * 8388608 +  1 * 6291456 + 26 * 8388608 +
1 * 1679309 + 38 * 8388608 +  1 * 1679309 + 38 * 8388608 +
1 * 5242880 + 37 * 8388608 +  1 * 3145728 + 31 * 8388608
```

Final total:

```
Total exfiltrated data: 5,449,252,456 bytes (~5.45 GB)
```

**Q9 – Attacker’s IP Address**

By filtering for `DeleteObject` events and inspecting the `sourceIPAddress` field, we found:

```
193.189.100.204
```

This suggests the attacker rotated IPs but used this one during file deletion attempts.

**Q10 – DeleteBucket Event and Request ID**

A single `DeleteBucket` operation was observed. The associated request ID:

```
XT27FP62J3ACKDNW
```

