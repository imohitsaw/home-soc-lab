# home-soc-lab
Home SOC lab: Kali, Windows/Sysmon, and Wazuh on an isolated network - detection and incident response practice for SOC analyst role.

# Overview

The lab is three virtual machines on an isolated network:

- ** Kali Linux ** - The attack machine, used to run scans and brute-force attacks.

- ** Windows 11** - The target machone, running ** Sysmon ** for detailed endpoint logging (process creation, network connection, etc.)

- ** Wazuh ** - The SIEM, deployed via Docker, collecting and alerting on logs from the target machine (Windows)

All three run on an Apple Silicon Mac using **UTM** for virtualization (instead of VirtualBox, which does not support Apple Silicon Natively) and ** Docker ** or Wazuh.

###### **-------- Tools Used --------**

| Tools -> Purpose |

UTM  -> Virtualization on Apple Silicon
Kali Linux -> Attacker Machine
Win 11 -> Target Machine
Sysmon -> Windows endpoint logging (process, network, file events)
Wazuh -> SIEM - log collection, corrleation and alerting
Docker -> Runs the Wazuh stack (Manager, indexer, dashboard)
NMAP -> Port scanning (Limux)
Hydra -> Attack tool (Linux)



