

# Google Cloud Logs Investigation Summary

We analyzed the logs to answer key questions regarding suspicious activity in the environment.

---

## 1. Compromised User Account

To identify compromised accounts, we filtered logs for authentication activity:

```bash
jq 'select(.protoPayload.authenticationInfo.principalEmail != null)' logs.json
```

We found a high number of authentication entries for the following Gmail address:

```
david.smith8391273718@gmail.com
```

This indicates that this user account was likely compromised.

---

## 2. Bucket Accessed

Filtering log events by the compromised user revealed access to the following Cloud Storage bucket:

```
confidential-documents-482374561
```

---

## 3. Sensitive File Accessed

Searching for object listing operations within the bucket showed the following sensitive file was accessed:

```
Financial_Report_2023_Classified.pdf
```

---

## 4. Compute Engine Instance Accessed

We found that the user accessed the following Compute Engine instance:

```
monitoring-instance
```

---

## 5. Service Accounts Involved

Searching for the compromised user's activity related to Compute Engine, we discovered two service accounts:

* `cloudops-service@hybrid-elixir-370815.iam.gserviceaccount.com`
* `cloud-ops-service@hybrid-elixir-370815.iam.gserviceaccount.com` (created later)

The second account appears to be a newer variant, possibly used for persistence.

---

## 6. Database Accessed

The user also interacted with the following resource, likely a database:

```
analytics-db
```

---

## 7. Service Account Creation

Log entries confirm that the attacker created a new service account:

```
cloud-ops-service
```

---

## 8. Service Account Key Created

The attacker also created a service account key for the above account. The key ID found:

```
16569af4fb98194fc210770d353a2513bada24b3
```

---

