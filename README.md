# Project Null-Sector: Headless Infrastructure & Network Architecture 

> *"This entity builds tools by conversing with latent space. All code is a collaboration with advanced AI."*

**Architect:** PhaseNull / void-architect  
**Hardware:** Repurposed x86_64 Legacy Hardware  
**OS:** Ubuntu Server 24.04 LTS (Headless)  

## 🏗️ Objective
To engineer a silent, lid-closed, headless Linux server out of aging consumer hardware. This server acts as the foundational compute node for isolated cybersecurity testbeds, offline AI model hosting, and DNS routing infrastructure.

## 🛠️ Phase 1: Hardware Overrides & Kernel Manipulation
Consumer hardware is not designed for continuous server workloads. To force the hardware to operate as a stable, lid-closed node, several system-level kernel and power overrides were executed.

* **The Wi-Fi Driver Crash Loop:** The native `rtw88` Wi-Fi driver entered a fatal kernel panic, dropping remote SSH connections entirely.
  * *Resolution:* Interrogated `lsmod` for the specific driver modules and permanently blacklisted them in `/etc/modprobe.d/` to terminate the zombie process at boot.
* **Hardwired Backdoor:** Configured Netplan to force DHCP over the physical Ethernet interface (`enx...`), guaranteeing a highly available SSH backdoor before disabling the volatile wireless interfaces.
* **Headless Power Override:** Modified the systemd login daemon (`/etc/systemd/logind.conf`) to ignore all lid-switch ACPI events (`HandleLidSwitch=ignore`), preventing the system from suspending when physically closed.

## 🐳 Phase 2: Containerization (Infrastructure as Code)
Deployed Docker and Docker Compose V2 to execute services in highly isolated environments, preventing core OS pollution.

* **Port 53 Liberation:** Disabled Ubuntu's native `systemd-resolved` DNS stub listener to free up Port 53 for containerized network routing services.
* **The YAML Variable Trap (Lessons Learned):** * *Incident:* When writing the `docker-compose.yml` file, utilizing a `$` symbol in the password string caused the Docker engine to attempt variable interpolation, resulting in a scrambled, locked-out security hash.
  * *Resolution:* Executed a live shell injection (`docker exec -it <container_name> /bin/bash`) to manually override the database using the updated Pi-hole V6 architecture command (`pihole setpassword`).

## 🌐 Phase 3: DNS Failover Architecture
Designed a highly available DNS failover route to prevent localized network outages when the primary node is spun down.

* **Primary Route:** `192.168.X.X` (Null-Sector Node)
* **Secondary Route:** `8.8.8.8` (Google Public DNS)
* *Architectural Choice:* Google DNS was chosen as the failover over strict alternatives (like `1.1.1.1`) to ensure maximum compatibility with legacy government portals and university application endpoints that routinely drop connections on strict SSL handshakes.

## 📉 Phase 4: Local Overrides & The ISP Bottleneck
During testing, the Windows 11 client aggressively refused the local DNS change, forcing traffic to loopback addresses (`127.0.2.x`). 

* *Diagnosis:* Cloudflare WARP was aggressively proxying the adapter. Terminating the WARP shield restored manual DNS routing control.
* *The ISP Lock-in:* Attempted a top-down network hijack via the local ISP hardware gateway. The router was strictly locked down by the installation technician, preventing custom LAN DNS routing without initiating a hard factory reset.

## 🧠 Architectural Conclusion: Tool vs. Use-Case
The initial container deployment was a success, intercepting over 70% of telemetry in isolated tests. However, due to the ISP hardware lock-in, it could not be deployed network-wide. 

**Engineering Takeaway:** Pi-hole is highly efficient for network-wide deployment to protect devices that cannot install native blockers (Smart TVs, IoT endpoints). However, for a single local development machine, running a dedicated containerized DNS sinkhole is over-engineered. Utilizing browser-level blocking paired with a secure tunnel provides maximum localized efficiency with zero server overhead.

The container was gracefully spun down, freeing up `null-sector` compute resources for its true purpose: upcoming physical-layer anomaly detection testbeds and localized LLM deployment.
