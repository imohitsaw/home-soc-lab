Wazuh Setup on Apple Silicon - Troubleshooting Log


Introduction --

Wazuh (the SIEM/log-monitoring piece of this lab) runs via Docker on Mac Host.
Getting it running surfaced two separate, genuine ARM64 compatibility issues-
documented here since both took real diagnosis to resolve.

Setup overview --

Wazuh official single-node Docker deployment was used.

git clone https://github.com/wazuh/wazuh-docker.git -b v4.9.0
cd wazuh-docker/single-node

The Deployment consists of three containers: wazuh.manager, wazuh.indexer, and wazuh.dashboard, plus a
one-time certificate-generation step required before the stack can start(the containers use TLS between each other).

Problem 1: Certificate generator kept failing. --

Running the official cert generation command gave me this error, over and over:

cp: cannot create regular file '/certificates/root-ca-manager.pem': Permission denied

This was happening because wazuh-certs-generator image is x86-only, so it runs under emulation on my Mac's ARM chip
- and that emulation was breaking the script file Permission. This a real, known bug(other ARM Mac users hit the exact same thing on Github)

Solution:

1. Swapped in a community-built ARM-native version of the generator (normisg/wazuh-certs-generator) instead of the official one.
2. That fixed most of it, but two files still failed because the folder itself had no write permission(chmod 755 fixed it).
3. The two last missing files I just created manually by copying the root

Problem 2: The Main Wazuh containers wouldn't run properly --

Even after fixing certificates, Docker kept warning that main wazuh images (manager/indexer/dashboard) do not match my Mac's chip.
Wazuh has not released ARM-native versions of those yet, so they have to run emulated but that requires Rosetta (Apple emulation tool)
Which I didnt have insatlled.

Solution: Installed Rosetta (softwareupdate --install --Rosetta), confirmed Dockers's Rosetta setting was on, restarted the containers.


Result:

All three containers came up clean. Logged into the dashboard at https://localhost using the password set in the docker-compose file.

what I learned

Both problems came from the same root cause- Wazuh assuming everyone on an Inte/Amd Chip but showed up differently: One was a Missing ARM-compatible image, 
the other was missing emulation tool. Reading the actual warning text closely (not just the surface error) was what pointed me to the real fix each time.

