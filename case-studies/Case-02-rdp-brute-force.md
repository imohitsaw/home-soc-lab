Case 02: RDP Brute Force Attack

Went after RDP on my Windows VM (win-target, 192.168.64.4) from Kali using Hydra with a Password list (rockyou.txt)

hydra -l win-target -P /usr/share/wordlists/rockyou.txt rdp://192.168.64.4

Attempt 1

Got a bunch of failed logins (Wazuh), but when I tried logging in myself right after, it just let me in no lockout. I was not sure why at the time. Checked the lockout policy in secpol.msc and confirmed it was actually configured(10 attempts, 10-minute reset), so lockout was not disabled, but I still don't know for certain why it did not trigger that run. Couldve been the reset window, could've been something about the VM's state, or possibly having just turned RDP on. Did'nt get a clean answer here, just moved on to the next attempt.

Troubleshooting attempt 2

Second run failed right away: [Error] freerdp: The connection failed to establish. Went through the usual suspects. IP had not changed, RDP services was running, firewall rules looked fine. Everything checked out, which was confusing. Tried connecting manually with xfreerdp instead, and it worked immediately. So the target was not the problem. Hydra RDP support is flagged as experimental by the tool itself, and this was just i being flaky.

Attempt 2 - confirmed lockout

Ran Hydra again, and this time Windows actually locked the account. Confirmed it myself trying to log in right after:

"The referenced account is currently locked out and may not be logged on to."

(screenshot: case02-windows-lockout.png)

Detection

Windows Security log:

- Event 4625 - failed logon, one per attempt.
- Event 4740 - account locked out

(screenshot: case02-event-4625.png)

Wazuh, via the agent, picked this up from the Security log (not Sysmon):

- Rule 60122 - Logon Failure (level 5)
- Rule 60204 - Multiple Windows Logon Failures (level 10)
- Rule 60115 - Account locked out (level 9), tagged T1110 - Brute Force

(screenshot: case02-wazuh-detection.png)

What I learned

Sysmon and Window's own Security log cover different things logons and lockouts only show up in the Security log, which is what Wazuh was acutally reading here. I also learned Wazuh does not just log individual events; it notices patterns and bumps up the severity when things look more like an attack than a typo. And honestly, the biggest lesson was not to assume a failure means the target is protected sometime it's just the tool. When I tested with xfreerdp using the known correct password, it logged in successfully proving RDP was fully reachable and functional, and the connection failure was coming from Hydra's tooling, not from Windows blocking anything.