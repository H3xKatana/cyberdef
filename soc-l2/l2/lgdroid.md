

Adversaries may execute active reconnaissance scans to gather information that can be used during targeting. In these scans, the adversary probes the victim's infrastructure via network traffic, as opposed to other forms of reconnaissance that do not involve direct interaction.

Adversaries may perform different forms of active scanning depending on what information they seek to gather. These scans can also be performed in various ways, including using native features of network protocols such as ICMP. Information from these scans may reveal opportunities for other forms of reconnaissance (e.g., Search Open Websites/Domains or Search Open Technical Databases), establishing operational resources (e.g., Develop Capabilities or Obtain Capabilities), and/or initial access (e.g., External Remote Services or Exploit Public-Facing Application).

---

This is the output of Dumpsy — not very readable by ALEAPP, more suited for manual analysis.

---

**Q1: What is the email address of Zoe Washburne?**
From agent data contact DB, we can find the email `zoewash@0x42.null`, or just grep the whole directory.

---

**Q2: Device date and time**
In live data: captured at **2021-05-21 18:17:56**.

---

**Q3: What time was Tor Browser downloaded (in UTC)?**
From the agent data downloads DB, we can find the time in UTC.

---

**Q4: When did the battery reach 100%?**
From `batterystats`, we can see that after the last reset +5m, the phone reached 100%.

---

**Q5: What is the password for the most recently connected Wi-Fi access point?**
I couldn't find this directly, and according to ChatGPT, this method is no longer standard — but I got a solution from the official walkthrough:

To uncover the password, check the file `com.android.providers.settings.data` located in `adb-data/apps/com.android.providers.settings/`.

---

**Q6: What app was the user focused on at 2021-05-20 14:13:27?**
Using usage-stats (equivalent to `/data/system/usagestats/`), and grepping for that timestamp, we find the user was using **YouTube**.

---

**Q7: How much time did the user spend on YouTube on that day?**
Grep for YouTube and check total usage time — about **8 hours 34 minutes**.
You can also calculate by checking when the app:

* `MOVE_TO_FOREGROUND` => app comes to the top/screen
* `MOVE_TO_BACKGROUND` => app is no longer in focus

---

**Q8: SSMI for the photos**
Using a random Python script from GitHub to calculate similarity between the given photo and the one found in storage: **similarity = 0.99**

