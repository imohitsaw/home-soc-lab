# home-soc-lab
Home SOC lab: Kali, Windows/Sysmon, and Wazuh on an isolated network - detection and incident response practice for SOC analyst role.

# Overview

The lab is three virtual machines on an isolated network:

- ** Kali Linux ** - The attack machine, used to run scans and brute-force attacks.

- ** Windows 11** - The target machone, running ** Sysmon ** for detailed endpoint logging (process creation, network connection, etc.)

- ** Wazuh ** - The SIEM, deployed via Docker, collecting and alerting on logs from the target machine (Windows)

All three run on an Apple Silicon Mac using **UTM** for virtualization (instead of VirtualBox, which does not support Apple Silicon Natively) and ** Docker ** or Wazuh.

**-------- Tools Used --------**

| Tools -> Purpose |

UTM  -> Virtualization on Apple Silicon
Kali Linux -> Attacker Machine
Win 11 -> Target Machine
Sysmon -> Windows endpoint logging (process, network, file events)
Wazuh -> SIEM - log collection, corrleation and alerting
Docker -> Runs the Wazuh stack (Manager, indexer, dashboard)
NMAP -> Port scanning (Limux)
Hydra -> Attack tool (Linux)

**-------- Case Studies --------**

Each case study covers one attack: what was run, what showed up in Wazuh (or didn't), and what that means.

- [Case 01 - NMAP Port Scan](case-studies/Case01-Port-Scan-(Nmap-SYN-Scan).md)
- [Case 02 - RDP brute Force Attack](case-studies/Case-02-rdp-brute-force.md)

-------- BUILD NOTES --------

- [Wazuh Setup on Apple Silicon - Troubleshooting Log](docs/01-wazuh-setup.md) - covers two real ARM64 compatibility issues hit while standing up Wazuh via Docker, 
and how they were fixed.

Getting this lab running on Apple Silicon (rather than a more commonly-documented Inte/VirutalBox setup) meant working through several platform specific issues that dont 
have well-established fixes online yet - including Wazuh's Docker Images assuming an amd64 host, and Windows blocking the Sysmon driver by default. Those are documented as they
were actually diagnosed and fixed, not just a final command list.


-------- How this was built --------

I used Claude as a guide throughout this project for the planning the approach, troubleshooting issues as they came up, and explaning concept until I actually understooding issues as
they came up, and explanining concepts until I actually understood them rather than just copying commands. Some explanation were crossed checked against the official documentation along the way.
Every command attack, and fix in this repo was something I ran and verified myslef, on my own lab.




