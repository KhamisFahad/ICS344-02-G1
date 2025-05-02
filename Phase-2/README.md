# Phase 2: SIEM Dashboard Analysis

In this phase, we capture network traffic on the victim machine, convert it into a format Splunk can ingest, upload it, and verify that the attack events appear in Splunk.

---

## 1. Attack Log from Attacker Side

Before collecting victim-side data, we confirm the exploit from the attacker’s perspective:

<figure>
  <img src="https://github.com/user-attachments/assets/e0535fe6-3278-419f-af95-d419a62d0ff9" alt="Attacker captures MS17-010 exploit logs" width="800"/>
  <figcaption><em>MS17-010 exploit being launched from the attacker machine; shows payload staging and handler setup.</em></figcaption>
</figure>

---

## 2. Start Network Trace on Victim

On the victim machine, we start a live network trace to record all SMB traffic:

<figure>
  <img src="https://github.com/user-attachments/assets/ea3cd103-a350-4adb-ab39-7fb065c8a378" alt="Running netsh trace start on victim" width="800"/>
  <figcaption><em>Executing <code>netsh trace start</code> on the victim to capture network events into an ETL file.</em></figcaption>
</figure>

---

## 3. Stop Trace and Convert ETL to TXT

After reproducing the attack, we stop the trace and convert the resulting ETL file to plain text:

<figure>
  <img src="https://github.com/user-attachments/assets/cd82f813-01eb-4289-a730-86a09bbe4383" alt="Converting ETL to TXT" width="800"/>
  <figcaption><em>Using <code>netsh trace stop</code> followed by <code>netsh trace convert input=NetTrace.etl output=NetTrace.txt</code> to produce a Splunk-readable .txt file.</em></figcaption>
</figure>

---

## 4. Transfer and Ingest Logs into Splunk

We serve the converted log file via a simple HTTP server on WSL and then add it to Splunk:

<figure>
  <img src="https://github.com/user-attachments/assets/a3085d73-e300-4a53-9a7d-8b7ee837e105" alt="Uploading NetTrace.txt to Splunk" width="800"/>
  <figcaption><em>In Splunk Web, under **Settings → Data Inputs → Files & directories**, we upload <code>NetTrace.txt</code> into the “ms17” index.</em></figcaption>
</figure>

---
