# SOLVING THE MURDER OF ROLAND GREENE 
__Solving the Murder of Roland Greene (SQL Investigation Project)__

## Project Overview

This project is a data-driven SQL investigation into the murder of Roland Greene, a wealthy art collector found dead in his private vault room at exactly 8:00 PM. Although all 30 guests claimed to have alibis, database evidence reveals that at least one suspect is lying.

Using structured query language (SQL), this project analyzes access logs, call records, and forensic timelines to uncover contradictions, reconstruct events, and identify the prime suspect.


## Objectives

- Reconstruct the timeline of events using SQL

- Verify suspects’ alibis against digital records

- Identify individuals present at the crime scene

- Cross-reference forensic data with movement logs

- Determine the most likely suspect based on evidence


## Tools Used
- Microsoft SQL Server (SSMS)
- SQL (Joins, filtering, time-based analysis)
- CSV datasets

## Dataset 
| Table                 | Description                           |
| --------------------- | ------------------------------------- |
| suspects_large        | Suspect identities, roles, and alibis |
| access_logs_records   | Door access records with timestamps   |
| call_records_large    | Incoming and outgoing call logs       |
| forensic_events_large | Timeline of forensic events           |

*The Datasets*
<a href="https://github.com/AyanfePortfolio/Solving-the-Murder-of-Roland-Greene-/blob/main/suspects_large.csv"> suspects_large | </a>
<a href="https://github.com/AyanfePortfolio/Solving-the-Murder-of-Roland-Greene-/blob/main/access_logs_large.csv"> access_logs_records | </a>
<a href="https://github.com/AyanfePortfolio/Solving-the-Murder-of-Roland-Greene-/blob/main/call_records_large.csv"> call_records_large | </a>
<a href="https://github.com/AyanfePortfolio/Solving-the-Murder-of-Roland-Greene-/blob/main/forensic_events_large.csv"> forensic_events_large </a>


## Task Queries 

### TASK 1: Database Schema SetUp 

__Create Suspects Table__  
CREATE TABLE suspects_large (  
    suspect_id INTEGER PRIMARY KEY,  
    name VARCHAR(50),  
    role VARCHAR(50),  
    relation_to_victim VARCHAR(50),  
    alibi VARCHAR(255)  
);  

__Create Access Logs Table__  
CREATE TABLE access_logs_records (  
    log_id INTEGER PRIMARY KEY,  
    suspect_id INTEGER,  
    access_time TIMESTAMP,  
    door_accessed VARCHAR(100),  
    success_flag BOOLEAN,  
    FOREIGN KEY (suspect_id) REFERENCES suspects_large(suspect_id) 
);  

__Create Call Records Table__  
CREATE TABLE call_records_large (  
    call_id INTEGER PRIMARY KEY,  
    suspect_id INTEGER,  
    call_time TIMESTAMP,  
    call_duration VARCHAR(20),  
    recipient_relation VARCHAR(100),  
    FOREIGN KEY (suspect_id) REFERENCES suspects_large(suspect_id)  
);  

__Create Forensic Events Table__  
CREATE TABLE forensic_events_large (  
    event_time TIMESTAMP,  
    event_description VARCHAR(255)  
);  
  
### TASK 2: Evidence-Based Investigation  
----------------------------------------------------------------------------
__Query 1: Vault Room Access Analysis__  
----------------------------------------------------------------------------
_Question: Who accessed the Vault Room shortly before or after 8:00 PM?_  
_Time Window: 7:55 PM - 8:05 PM_  

SELECT 
    s.name, s.role, s.relation_to_victim, a.access_time, a.door_accessed, a.success_flag  
FROM access_logs_records AS a  
JOIN suspects_large AS s  
    ON a.suspect_id = s.suspect_id  
WHERE a.access_time BETWEEN '2025-06-01 19:55:00' AND '2025-06-01 20:05:00'  
    AND a.door_accessed = 'Vault Room'  
    AND a.success_flag = 'true'  
ORDER BY a.access_time;  

__FINDINGS:__  
_Three individuals accessed the Vault Room:_  
1. Robin Ahmed (Family Doctor) - 7:56:35 PM (BEFORE murder)  
2. Jamie Bennett (Cleaner) - 8:00:55 PM (AFTER murder - 55 seconds)  
3. Victor Shaw (PR Manager) - 8:04:53 PM (AFTER murder - 4 min 53 sec)  

----------------------------------------------------------------------------
__Query 2: Final Phone Call Analysis__  
----------------------------------------------------------------------------
_Question: What does the call log reveal about Roland's final phone calls?_  
_Focus: Calls to the victim around 7:55 PM_  

SELECT 
    s.name, s.role, s.relation_to_victim, c.call_time, c.call_duration, c.recipient_relation  
FROM call_records_large AS c  
JOIN suspects_large AS s  
    ON c.suspect_id = s.suspect_id  
WHERE c.call_time BETWEEN '2025-06-01 19:55:00' AND '2025-06-01 20:05:00'  
    AND c.recipient_relation = 'Victim'  
ORDER BY c.call_time;  

__FINDINGS:__  
_Roland received two calls before his death at 8:00 PM:_ 
1. Victor Hale (Driver) - 7:55:45 PM, Duration: 1 minute  
2. Susan Knight (Chef) - 7:56:39 PM, Duration: 6 minutes  

_CRITICAL INSIGHT: Susan Knight's 6-minute call starting at 7:56:39 PM would have ended around 8:02:39 PM - meaning she was likely ON THE PHONE when the gunshot occurred at 8:00 PM. She may have heard the murder._  


----------------------------------------------------------------------------
__Query 3: Alibi Verification Against Access Logs__
----------------------------------------------------------------------------
_Question: Are there inconsistencies between door access and alibi claims?_

SELECT  
    s.name, s.role, s.relation_to_victim, a.access_time, a.door_accessed, a.success_flag, s.alibi  
FROM access_logs_records AS a  
JOIN suspects_large AS s  
    ON a.suspect_id = s.suspect_id  
WHERE a.access_time BETWEEN '2025-06-01 19:55:00' AND '2025-06-01 20:05:00'  
    AND a.door_accessed = 'Vault Room'  
    AND a.success_flag = 'true'  
ORDER BY a.access_time;  

_ALIBI CONTRADICTIONS IDENTIFIED:_ 
1. ROBIN AHMED (Family Doctor) - 7:56:35 PM  
   Alibi: "Left early"  
   Reality: Access logs show he was IN the Vault Room during the murder window  
   Status: FALSE ALIBI - PRIMARY SUSPECT  
  
2. JAMIE BENNETT (Cleaner) - 8:00:55 PM  
   Alibi: "At home"  
   Reality: Entered Vault Room 55 seconds after gunshot  
   Status: FALSE ALIBI - Likely discovered the body or witnessed aftermath  
  
3. VICTOR SHAW (PR Manager) - 8:04:53 PM  
   Alibi: "At hospital"  
   Reality: Accessed Vault Room after security feed was cut  
   Status: FALSE ALIBI - Arrived after the crime  

----------------------------------------------------------------------------
__Query 4: Forensic Timeline Reconstruction__  
----------------------------------------------------------------------------
_Question: What is the complete forensic timeline?_  

SELECT  
    event_time, event_description  
FROM forensic_events_large  
ORDER BY event_time ASC;  

FORENSIC TIMELINE:  
- 7:57:00 PM - Victim last seen alive  
- 8:00:00 PM - Gunshot heard  
- 8:01:15 PM - Motion detected in Vault Hallway  
- 8:03:00 PM - Security feed cut  
- 8:05:45 PM - Emergency call placed from mansion  


### TASK 3: Deep Dive Analysis  
----------------------------------------------------------------------------
__Query 5: Movement Pattern Analysis__  
----------------------------------------------------------------------------
_Question: Which suspect movements overlap with critical forensic events?_  

SELECT  
    s.name, a.door_accessed, a.access_time, s.alibi, f.event_description, f.event_time  
FROM access_logs_records AS a  
JOIN suspects_large AS s  
    ON a.suspect_id = s.suspect_id  
JOIN forensic_events_large AS f  
    ON a.access_time BETWEEN f.event_time - INTERVAL '1 minute'  
                          AND f.event_time + INTERVAL '1 minute'  
WHERE a.door_accessed = 'Vault Room'  
ORDER BY a.access_time ASC;  

_CORRELATION ANALYSIS:_  

ROBIN AHMED - 7:56:35 PM  
- Entered Vault Room moments before "victim last seen alive" at 7:57 PM  
- Was WITH Roland just before the gunshot at 8:00 PM  
- Alibi ("Left early") CONTRADICTED by access logs  
- ONLY person confirmed with victim during murder window  
  
JAMIE BENNETT - 8:00:55 PM  
- Entered 55 seconds after gunshot  
- Likely triggered motion sensor at 8:01:15 PM (20 seconds after entry)  
- Alibi ("At home") CONTRADICTED  
- Timeline suggests she DISCOVERED the body, not committed murder  
  
VICTOR SHAW - 8:04:53 PM  
- Entered AFTER security feed cut at 8:03 PM  
- Shortly before emergency call at 8:05:45 PM  
- Alibi ("At hospital") CONTRADICTED  
- Arrived well after crime occurred  

## Investigation Conclusion  

__KILLER IDENTIFIED: ROBIN AHMED (Family Doctor)__  
  
EVIDENCE SUMMARY:  
1. Only suspect confirmed inside Vault Room with victim during murder window  
2. Access log shows entry at 7:56:35 PM - moments before victim last seen alive  
3. Was present during critical time between 7:56 PM and 8:00 PM gunshot  
4. Alibi "Left early" directly contradicted by digital evidence  
5. Had opportunity and access to commit the crime  

SUPPORTING EVIDENCE:  
- No other suspect can be placed with the victim during murder window  
- Jamie Bennett arrived after murder (discovered body)  
- Victor Shaw arrived even later (possibly to help cover up)  
- Susan Knight was on phone but not physically present in Vault Room  

__RECOMMENDATION: Arrest Robin Ahmed for the murder of Roland Greene.__  

 
 __|  END OF INVESTIGATION  |__  
 
