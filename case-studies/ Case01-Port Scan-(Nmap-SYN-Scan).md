Case 01: Port Scan (Nmap SYN Scan)

What I did

Ran a full SYN scan from Kali against my Windows VM (win-target, 192.168.64.4), checking all 65,535 ports:

nmap -sS -p- 192.168.64.4

What I found

Only one port came back open:

PORT     STATE SERVICE
7680/tcp open  pando-pu

Everything else showed as filtered - Windows's Firewall was not responding to the probes at all, not even with a "closed" reply.
Port 7680 turned out to be Windows Update's Delivery Optimization feature (peer-to-peer update sharing between Windows machine on the
same network) - not a login service, so nothing there to actually attack.

Why it was not detected

I checked both Wazuh and Sysmon's own local log afterward, and found nothing related to the scan no alerts, no matching events. The reason came down to how a
SYN scan actually works. It sends a connection request and never finishes the handshake, so from Window's point of view nothing ever really "connects". Sysmon's network logging rule (Event ID 3) only records connections that a local process actually completes a half-open probe from outside never triggers it. When I looled at Sysmon's log around that time, all I found were unrelated entries (OneDrive syncing in the background) real proof that Sysmon was working, just not built to catch this particular technique.

(Screenshot: case01-Sysmon-log.png)

What I learned

This one suprised me a bit first I expected some kind of alert just from scanning every port on the machine. But it made sense once I understood what Sysmon is actually designed to watch: activity happening on the machine, not everything that arrives at the network card. A stealthy scan like this would need a different kind of tool to catch something watching network traffic directly, like an IDS, rather than a host-based tool Sysmon. Good reminder that no single tool sees everything, and detection coverage has real gaps worth knowing about. 