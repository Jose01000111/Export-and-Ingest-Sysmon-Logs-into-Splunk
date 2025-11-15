# 🚀Phase-VII — Splunk SOC Lab (Windows 11, Suricata + Sysmon + Docker)

In this lab, I captured **Windows events via Sysmon** and **network events via Suricata**, and ingested them into **Splunk running in Docker** for analysis. I kept my Windows host clean while still gathering logs effectively.

# Start Splunk Docker 🚀
I started by spinning up **Splunk in Docker** so I could isolate it from my Windows host.
>- Started or launched Splunk container — Docker basics
>- Accessed web UI for log ingestion — Splunk interface

#  Install Sysmon 🖥️
I downloaded **Sysmon** and a recommended configuration, installed it, and verified that the service was running. Windows event logging was now actively capturing process creation, network connections, and more.
>- Sysmon — Windows event monitoring
>- sysmonconfig.xml — recommended configuration for logging

#  Install Npcap 🌐
I installed **Npcap** to allow Suricata to capture live network traffic on my host.
>- Npcap — packet capture driver for Windows
>- WinPcap API mode — compatibility for Suricata

#  Install Suricata 🕵️‍♂️
I installed **Suricata IDS/IPS** to capture network traffic and log it for analysis.
>- Suricata — network IDS/IPS
>- Network adapter selection — live packet capture

#  Suricata Logging 📂
I set up Suricata logging so that network events would be written to a folder I could later copy into Splunk.
>- eve.json — Suricata event log
>- stats.log — Suricata statistics

#  Copy Suricata Logs into Splunk Docker 📥
I copied the Suricata logs into the Splunk container to make them available for ingestion.
>- docker cp — move logs into container
>- /tmp/SuricataLogs — folder for monitoring in Splunk

#  Export Sysmon Logs 📊
I exported **Sysmon event logs** so that host activity could be analyzed in Splunk.
>- wevtutil — Windows event export tool
>- C:\SysmonLogs — folder for exported logs
>- docker cp — move logs into Splunk container

#  Add Data to Splunk 📈
I added both **network and host logs** to Splunk so I could search and analyze them.
>- Suricata logs — source type `suricata`
>- Sysmon logs — source type `WinEventLog:Sysmon`
>- os_logs — index for all logs

# Observations 💡
>- Docker keeps Splunk isolated from my Windows host — easy to reset or rebuild
>- Exported Sysmon logs allow portable Windows activity records
>- Suricata logs provide real-time network monitoring insights
