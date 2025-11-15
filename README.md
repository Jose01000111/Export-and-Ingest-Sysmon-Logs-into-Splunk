# Lab GitHub Post — Splunk SOC Lab (Windows 11, Suricata + Sysmon + Docker)

In this lab, I’m capturing both **Windows events via Sysmon** and **network events via Suricata**, and ingesting them into **Splunk running in Docker** for analysis.  

OS / CPU: Windows 11 Pro, 22H2, x64  
Splunk Docker: `splunk/splunk:latest` (Ports: 8000, 9997)  
Log Index: `os_logs`  
Sources: Sysmon, Suricata

---

# Phase 1 — Start Splunk Docker 🚀
I started by spinning up **Splunk in Docker** so I could keep my Windows host clean.

## Starting and Verifying Splunk
>- \\[Check containers — `docker ps -a`]  
>- \\[Start container — `docker start splunk-lab`]  
>- \\[Launch new container — `docker run -d -p 127.0.0.1:8000:8000 -p 127.0.0.1:9997:9997 -e SPLUNK_START_ARGS=--accept-license -e SPLUNK_USERNAME=admin -e SPLUNK_PASSWORD=<YourPassword> --name splunk-lab splunk/splunk:latest`]  
>- \\[Login — Browser → `http://127.0.0.1:8000` → admin / <password>]  

---

# Phase 2 — Install Sysmon (Windows Host)
I needed **Sysmon** to capture detailed Windows event logs.

## Installing Sysmon
>- \\[Create folder — `mkdir C:\Sysmon`]  
>- \\[Download Sysmon — `Invoke-WebRequest "https://download.sysinternals.com/files/Sysmon.zip" -OutFile "Sysmon.zip"`]  
>- \\[Extract files — `Expand-Archive Sysmon.zip -DestinationPath .`]  
>- \\[Download recommended config — `Invoke-WebRequest "https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml" -OutFile "sysmonconfig.xml"`]  
>- \\[Install Sysmon — `.\Sysmon64.exe -accepteula -i sysmonconfig.xml`]  
>- \\[Verify service — `Get-Service Sysmon64`]  

✅ Sysmon is now logging Windows events.

---

# Phase 3 — Install Npcap (Required for Suricata)
Npcap is needed to capture **network packets** for Suricata.

## Installing Npcap
>- \\[Download Npcap from official site]  
>- \\[Install — Enable WinPcap API-compatible mode]  
>- \\[Optional — Enable loopback capture]  
>- \\[Reboot host if prompted]

---

# Phase 4 — Install Suricata (Windows Host)
Suricata monitors **network traffic** in real-time.

## Installing Suricata
>- \\[Download Windows MSI installer from Suricata official downloads]  
>- \\[Install and finish]  
>- \\[Verify installation — `& "C:\Program Files\Suricata\suricata.exe" -V"`]  

Expected output: version number (e.g., 8.0.2)

---

# Phase 5 — Suricata Logging
I set up Suricata to capture traffic and generate logs.

## Step 5.1 — Create log folder
>- \\[New-Item — `New-Item -Path "C:\SuricataLogs" -ItemType Directory -Force`]  

## Step 5.2 — Identify network adapter
>- \\[List adapters — `Get-NetAdapter | Select Name, InterfaceIndex, Status`]  
>- \\[Choose active Ethernet adapter (e.g., Ethernet 2)]  

## Step 5.3 — Run Suricata
>- \\[Run — `& "C:\Program Files\Suricata\suricata.exe" -c "C:\Program Files\Suricata\suricata.yaml" -l "C:\SuricataLogs" -i "Ethernet 2"`]  
>- \\[Logs generated — `C:\SuricataLogs\eve.json` and `stats.log`]  

## Step 5.4 — Generate test traffic
>- \\[Ping — `ping google.com`]  
>- \\[Or browse websites — generate network events]  

## Step 5.5 — Verify logs
>- \\[Check files — `dir C:\SuricataLogs`]  
Expected: `eve.json`, `stats.log`

---

# Phase 6 — Copy Suricata Logs into Splunk Docker
Now I made Suricata logs accessible to Splunk.

## Copying Logs
>- \\[Command — `docker cp C:\SuricataLogs splunk-lab:/tmp/SuricataLogs`]  
>- \\[Splunk path — `/tmp/SuricataLogs`]  
>- \\[Ready for ingestion — Splunk can monitor this folder]

---

# Phase 7 — Export Sysmon Logs (Windows Host)
I exported **Sysmon event logs** to ingest into Splunk.

## Create folder for Sysmon logs
>- \\[Folder — `mkdir C:\SysmonLogs`]  

## Export Sysmon Event Logs
>- \\[Command — `wevtutil epl Microsoft-Windows-Sysmon/Operational C:\SysmonLogs\Sysmon.evtx`]  
>- \\[EVTX format — native Windows event log format]  

## Copy logs into Splunk Docker
>- \\[Command — `docker cp C:\SysmonLogs splunk-lab:/tmp/SysmonLogs`]  
>- \\[Splunk path — `/tmp/SysmonLogs`]  

---

# Phase 8 — Add Data to Splunk
Finally, I added both **Suricata and Sysmon logs** to Splunk.

## Add Suricata Logs
>- \\[Splunk UI → Settings → Add Data → Monitor → Files & Directories]  
>- \\[Folder — `/tmp/SuricataLogs`]  
>- \\[Source type — `suricata`]  
>- \\[Index — `os_logs`]  

## Add Sysmon Logs
>- \\[Splunk UI → Settings → Add Data → Monitor → Files & Directories]  
>- \\[Folder — `/tmp/SysmonLogs`]  
>- \\[Source type — `WinEventLog:Sysmon`]  
>- \\[Index — `os_logs`]  

---

# Observations
This lab helped me integrate **host and network telemetry** into a single Splunk instance.

>- \\[Containers are convenient — I can restart Splunk without touching my host]  
>- \\[Sysmon EVTX export — Windows event logs are portable for analysis]  
>- \\[Suricata — real-time network monitoring is now visible in Splunk]  
>- \\[Paths & permissions — key lesson, especially Windows → Docker file copy]  

