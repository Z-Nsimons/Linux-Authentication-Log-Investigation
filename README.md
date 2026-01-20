# Linux Authentication Log Investigation

## Overview
This project focuses on analyzing Linux authentication logs to identify failed and successful login attempts and to automate detection of suspicious authentication behavior.
The investigation was conducted in a controlled Unbuntu lab environment to simulate real-world defensive security analysis.

## Tools Used
- UTM
- Bash
- Linux authentication logs (/var/log/auth.log)
- Core Linux utilities (grep, awk, sort, uniq, wc)

## Objectives
- Identify failed and successful authentication attempts
- Understand how Linux logs authentication events
- Distinguish benign user error from potential brute-force behavior
- Automate detection of repeated failed login attempts

## Investigation Steps

### 1. Manual Log Review
Authentication logs were examined using:
*sudo less /var/log/auth.log*

The following events were identified:
- Failed login attempts due to incorrect username and password
- PAM authentication failure messages
- Successful login sessions
- Privilege escalation events using sudo

![Authentication Log Overview](screenshots/root_entries)

### 2. Failed Login Quantification
Failed login attempts were counted and reviewed with:
*sudo grep "FAILED_LOGIN" /var/log/auth.log*
*sudo grep "FAILED_LOGIN" /var/log/auth.log | wc -l*

![Failed Login Count and Timestamps](screenshots/num_fails)

### 3. Successful Authentication Review
Successful logins were identified to provide context:
*sudo grep "session opened for user" /var/log/auth.log*

![Successful Login Events](screenshots/successful_logins)

This confirmed that failed attempts were followed by a legitimate login, indicating user error rather than malicious activity.

## Automated Detection Script
To improve efficiency, a Bash script was developed to automate detection of failed login attempts.

### Script Functionality
The script:
- Parses *var/log/auth.log*
- Counts total failed login attempts
- Aggregates failures by username

![Bash Script](screenshots/bash_script)
![Script Execution Output](screenshots/script_run)

## Findings
- Failed login attempts occurred within a short time window
- Attempts originated from the same terminal (/dev/tty1)
- A successful login followed the failed attempts
- No evidence of brute-force behavior was observed

## Detection Analysis
This activity would not trigger a brute-force alert in a production environment due to:
- Low number of failed attempts
- Single source terminal
- Successful authentication shortly after failures

In a real SOC environment, alerts would typically trigger after multiple failures across different users or IP addresses within a short timeframe.

## Defensive Controls
In production environments, tools such as **Fail2Ban** can be used to:
- Monitor authentication logs
- Automatically block sources after repeated failures
- Reduce brute-force attack exposure
