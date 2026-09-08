# Wazuh Home SOC Lab

A self-hosted Security Operations Center lab built with Wazuh, demonstrating endpoint monitoring, File Integrity Monitoring (FIM), and security configuration assessment against a real Windows endpoint.

---

## Environment

- **Wazuh Manager** — deployed on Ubuntu (VMware), version 4.12.0
- **Windows 10 Pro endpoint** — agent ID `005`, hostname `WINDOWS22`, IP `192.168.159.133`
- Agent installed via the official MSI installer over PowerShell, registered to the manager at `192.168.159.130`
- Communication over the standard Wazuh agent-manager channel

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.12.0-1.msi -OutFile $env:tmp\wazuh-agent; msiexec.exe /i $env:tmp\wazuh-agent /q WAZUH_MANAGER='192.168.159.130' WAZUH_AGENT_NAME='WINDOWS22'
NET START WazuhSvc
```

---

## What's configured

### File Integrity Monitoring (FIM)
`ossec.conf` on the agent monitors a custom directory in real time, alongside Wazuh's default Windows monitoring paths (system directories, registry keys, startup folders):

```xml
<syscheck>
  <disabled>no</disabled>
  <frequency>43200</frequency>
  <directories realtime="yes">C:\Users\Public\Test</directories>
  ...
</syscheck>
```

### Security Configuration Assessment (SCA)
SCA scans run every 12 hours against the **CIS Microsoft Windows 10 Enterprise Benchmark v1.12.0**.

### Rootcheck
Enabled, checking Windows applications and malware indicators against Wazuh's default ruleset.

---

## Detection demo

A test file was created, modified, and deleted inside the monitored directory to trigger and verify FIM alerts:

```powershell
echo "test" > C:\Users\Public\test.txt
echo "change" >> C:\Users\Public\test.txt
del C:\Users\Public\test.txt
```

This produced three real alerts on the manager, visible in the Wazuh dashboard:

| Timestamp | Event | Rule ID | Rule description | Level |
|---|---|---|---|---|
| 02:51:52 | modified | 550 | Integrity checksum changed | 7 |
| 02:52:02 | modified | 550 | Integrity checksum changed | 7 |
| 02:52:10 | deleted | 553 | File deleted | 7 |

Full alert detail for one of the modification events (`c:\users\public\test.txt`):

```
decoder.name: syscheck_integrity_changed
rule.description: Integrity checksum changed.
rule.id: 550
rule.groups: ossec, syscheck, syscheck_entry_modified, syscheck_file
rule.gdpr: II_5.1.f
rule.hipaa: 164.312.c.1, 164.312.c.2

Changed attributes: size, mtime, md5, sha1, sha256
Size changed from '14' to '30'
Old modification time was: '1775433111', now it is '1775433121'
Old md5sum was: '4582c40a0b89549aa5eae9dc756985ec'
```

Wazuh's built-in rule mapping already tags this event against GDPR and HIPAA compliance references — visible directly in the alert metadata above, no manual mapping needed for those two.

---

## Dashboard views

- **Endpoints summary** — confirms agent `WINDOWS22` (005) active, Wazuh v4.12.0, group `default`
- **Agent overview** — MITRE ATT&CK tactic breakdown for the agent (Impact, Defense Evasion, Initial Access, Persistence hits from the default ruleset), plus PCI DSS compliance rollup
- **Events count evolution** — alert volume over time for the agent

---

## Screenshots

See the [`Screenshots`](./Screenshots) folder:

| File | Shows |
|---|---|
| `Change config file.png` | `ossec.conf` FIM/SCA/rootcheck configuration |
| `Commands for windows.png` | Wazuh agent install command reference |
| `Creation of test file.png` | Monitored directory with test file created |
| `Details of the file change event.png` | Full alert detail for a FIM modification event (rule 550) |
| `Endpoints(1 active agent).png` | Agent registration and active status |
| `Events in File Intgrity Monitoring....png` | FIM event table — create/modify/delete detections |
| `Overview.png` | Wazuh platform dashboard (agent summary, alert severity breakdown) |
| `Windows Agent Details.png` | Agent-specific dashboard — MITRE ATT&CK, SCA, compliance |
| `Windows powershell.png` | Commands used to trigger the test FIM events |

---

## Skills demonstrated

- SIEM deployment and agent-manager configuration (Wazuh, Ubuntu, Windows)
- File Integrity Monitoring — configuration, real-time detection, alert analysis
- Reading and interpreting SIEM alert data (rule IDs, decoders, full logs, checksums)
- Security Configuration Assessment against an industry benchmark (CIS)
- Basic familiarity with compliance/framework tagging as surfaced by SIEM tooling (GDPR, HIPAA, PCI DSS references built into Wazuh's default ruleset)

---

## Tools used

- Wazuh 4.12.0 (Manager + Agent)
- Ubuntu (manager host)
- Windows 10 Pro (monitored endpoint)
- PowerShell
