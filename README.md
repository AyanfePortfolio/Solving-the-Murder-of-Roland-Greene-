# Solving-the-Murder-of-Roland-Greene
Solving the Murder of Roland Greene (SQL Investigation Project)

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
<a href="https://github.com/AyanfePortfolio/Solving-the-Murder-of-Roland-Greene-/blob/main/suspects_large.csv"> suspects_large </a>
<a href="https://github.com/AyanfePortfolio/Solving-the-Murder-of-Roland-Greene-/blob/main/access_logs_large.csv"> access_logs_records </a>
<a href="https://github.com/AyanfePortfolio/Solving-the-Murder-of-Roland-Greene-/blob/main/call_records_large.csv"> call_records_large </a>
<a href="https://github.com/AyanfePortfolio/Solving-the-Murder-of-Roland-Greene-/blob/main/forensic_events_large.csv"> forensic_events_large </a>


## Task Queries 

### Task 1: Database Schema SetUp 

** Create Suspects Table**
CREATE TABLE suspects_large (
    suspect_id INTEGER PRIMARY KEY,
    name VARCHAR(50),
    role VARCHAR(50),
    relation_to_victim VARCHAR(50),
    alibi VARCHAR(255)
);

