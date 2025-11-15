# 🧠 Phase 7 — Export and Ingest Sysmon Logs into Splunk

In this phase, I focused on **getting Windows Sysmon logs into my Splunk Docker container** so I can analyze them alongside Suricata logs. It’s exciting because now I can correlate system events with network events in one place! 🔍

## Export Sysmon Logs (Windows Host)
I first needed to **export the Sysmon event logs** to a file so Splunk could read them.

- `mkdir C:\SysmonLogs` — Created a folder for exported logs  
- `wevtutil epl Microsoft-Windows-Sysmon/Operational C:\SysmonLogs\Sysmon.evtx` — Exported Sysmon logs to an EVTX file  

> 💡 Note: If you get a path error, make sure the folder exists and you have admin permissions.

## Copy Logs into Splunk Docker
Next, I copied the exported logs into the Splunk container so it could monitor them.

- `docker cp C:\SysmonLogs splunk-lab:/tmp/SysmonLogs` — Copied Windows logs into Docker  

> ✅ Now the logs live in `/tmp/SysmonLogs` inside the container, ready for Splunk ingestion.

## Add Data to Splunk UI
Finally, I told Splunk where to find the Sysmon logs.

- Splunk UI → **Settings → Add Data → Monitor → Files & Directories**  
- Folder: `/tmp/SysmonLogs`  
- Source type: `WinEventLog:Sysmon`  
- Index: `os_logs`  

> Now I can search `index=os_logs sourcetype="WinEventLog:Sysmon"` in Splunk to see system events like process creation, network connections, and file activity.  

## Observations
- It feels powerful seeing **Sysmon events in Splunk** alongside Suricata logs.  
- I learned to always check **folder paths and permissions** on Windows before exporting logs.  
- Using Docker keeps Splunk isolated and easy to reset if something breaks. 🚀
