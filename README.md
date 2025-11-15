# 🚀Phase-VII — Splunk SOC Lab (Windows 11, Suricata + Sysmon + Docker)

In this lab, I captured **Windows events via Sysmon** and **network events via Suricata**, and ingested them into **Splunk running in Docker** for analysis. I kept my Windows host clean while still gathering logs effectively.

# Start Splunk Docker 🚀
## I started by spinning up **Splunk in Docker** so I could isolate it from my Windows host.

<img width="802" height="116" alt="W8PHLAu" src="https://github.com/user-attachments/assets/522649bc-64eb-4cd2-9807-cbec7a1c4e29" />

>- Started or launched Splunk container — Docker basics
>- Accessed web UI for log ingestion — Splunk interface

#  Install Sysmon 🖥️
## I downloaded **Sysmon** and a recommended configuration, installed it, and verified that the service was running. Windows event logging was now actively capturing process creation, network connections, and more.

<img width="857" height="467" alt="SzN65Sv" src="https://github.com/user-attachments/assets/d6b07bd9-6bdf-4108-bfab-39b4efedc3d2" />

>- Sysmon — Windows event monitoring
>- sysmonconfig.xml — recommended configuration for logging

# Install Npcap 🌐
I downloaded and installed **Npcap from the official website** so Suricata could capture live network traffic on my Windows host.

<img width="492" height="393" alt="3qDdp6s" src="https://github.com/user-attachments/assets/e728faa1-2bda-492a-8b67-099f2febf5ba" />

<img width="857" height="467" alt="SzN65Sv" src="https://github.com/user-attachments/assets/1a97c63c-0776-433a-8690-9488335ff756" />

>- Npcap — packet capture driver for Windows, installed via web installer
>- WinPcap API mode — enabled for compatibility with Suricata

#  Install Suricata 🕵️‍♂️
I installed **Suricata IDS/IPS** to capture network traffic and log it for analysis.

<img width="686" height="74" alt="uFbVtUn" src="https://github.com/user-attachments/assets/f97bc710-e504-4502-a1f2-a9ca118902d3" />

>- Suricata — network IDS/IPS
>- Network adapter selection — live packet capture

#  Suricata Logging 📂
I set up Suricata logging so that network events would be written to a folder I could later copy into Splunk.

<img width="493" height="38" alt="RzuFbLc" src="https://github.com/user-attachments/assets/781b64e4-ca77-42ca-abc2-cd0acb97350f" />

---

<img width="1207" height="315" alt="e4vUGf6" src="https://github.com/user-attachments/assets/441fd7b0-f4a7-4f1e-bd04-ceb39e25d03f" />

>- eve.json — Suricata event log
>- stats.log — Suricata statistics

#  Copy Suricata Logs into Splunk Docker 📥
I copied the Suricata logs into the Splunk container to make them available for ingestion.

<img width="603" height="67" alt="gc2AC3Y" src="https://github.com/user-attachments/assets/b1b4af6e-8765-4bd1-9f7f-2a37968fccf1" />

---

<img width="826" height="324" alt="Hc93M4X" src="https://github.com/user-attachments/assets/9e678c15-4259-4d23-a4ec-b0e1e958d936" />

>- docker cp — move logs into container (Didnt capture)
>- /tmp/SuricataLogs — folder for monitoring in Splunk

#  Export Sysmon Logs 📊
I exported **Sysmon event logs** so that host activity could be analyzed in Splunk.

<img width="775" height="91" alt="E80TcNe" src="https://github.com/user-attachments/assets/d2df00e7-1793-476c-b8aa-6a6d2d19e1bd" />

---

<img width="565" height="228" alt="698oHmr" src="https://github.com/user-attachments/assets/89264db7-f58f-41fe-b275-847759ee3748" />


>- wevtutil — Windows event export tool
>- C:\SysmonLogs — folder for exported logs
>- docker cp — move logs into Splunk container


# Observations 💡
>- Docker keeps Splunk isolated from my Windows host — easy to reset or rebuild
>- Exported Sysmon logs allow portable Windows activity records
>- Suricata logs provide real-time network monitoring insights
