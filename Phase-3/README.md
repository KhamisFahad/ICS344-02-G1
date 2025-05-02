# Phase 3: Defensive Strategy Proposal

To defend against the MS17-010 SMB exploit, we can implement different approaches like:

- **Patch MS17-010 (KB4012212 / KB4012215)**  
  *Vendor security patch; eliminates the vulnerable code path.*
- **Disable SMBv1 & Block TCP 445**  
  *Configuration hardening; shrinks attack surface on the host.*
- **Network IPS rule in pfSense**  
  *Inline perimeter blocking of malicious SMB traffic.*

---

## 1. Pre-Hardening Vulnerability Scan

<figure>
  <img src="https://github.com/user-attachments/assets/9d43c253-c22a-42d7-b990-c9e5b6e9afd5" alt="Nmap MS17-010 vulnerability scan" width="800"/>
  <figcaption><em>We confirmed the vulnerability by running <code>nmap --script smb-vuln-ms17-010</code>, which showed the victim was exploitable.</em></figcaption>
</figure>

---

## 2. Disable SMBv1 & Block TCP Port 445

We executed the following commands on the victim machine to disable SMBv1 and block incoming SMB connections:

```powershell
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" /v SMB1 /t REG_DWORD /d 0 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Services\mrxsmb10" /v Start /t REG_DWORD /d 4 /f
netsh advfirewall firewall add rule name="Block TCP 445" dir=in action=block protocol=TCP localport=445
shutdown /r /t 0
```

<figure>
  <img src="https://github.com/user-attachments/assets/5ba9d708-14e6-4e2b-ac30-1320fa0ae8b2" alt="Disabling SMBv1 and blocking TCP 445" width="800"/>
  <figcaption><em>Disabled SMBv1, added a firewall block for port 445, and rebooted the machine.</em></figcaption>
</figure>

3. Post-Hardening Verification

<figure>
  <img src="https://github.com/user-attachments/assets/a9ee6cd2-0cdf-492b-8b82-65461a8e2a9e" alt="Nmap scan showing no MS17-010 vulnerability" width="800"/>
  <figcaption><em>Re-ran the MS17-010 NSE script and confirmed the service is no longer vulnerable.</em></figcaption>
</figure>
