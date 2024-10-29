# Comprehensive API Security Testing Guide

A detailed technical guide for API security testing, covering all aspects of reconnaissance, attack vectors, and security vulnerabilities.

## Table of Contents
- [Active Reconnaissance](#active-reconnaissance)
- [Passive Reconnaissance](#passive-reconnaissance)
- [Authentication Attacks](#authentication-attacks)
- [Authorization Attacks](#authorization-attacks)
- [Endpoint Analysis](#endpoint-analysis)
- [Improper Asset Management](#improper-asset-management)
- [Mass Assignment Attacks](#mass-assignment-attacks)
- [SSRF Exploitation](#ssrf-exploitation)


## Active Reconnaissance

Active reconnaissance involves directly interacting with the target through scanning. We use recon to search for target APIs and gather useful information.

### Nmap
General detection:
```bash
nmap -sC -sV [target address or network range] -oA nameofoutput
```
- `-sC`: Enables default scripts
- `-sV`: Enables service enumeration

All ports scan:
```bash
nmap -p- [target address] -oA allportscan
```

### Amass
OWASP Amass is a powerful command-line tool that maps a target's external network by collecting OSINT from over 55 different sources.

### Gobuster
Gobuster is used for brute-forcing URIs and DNS subdomains from the command line. It uses wordlists for common directories and subdomains to automatically request every item and filter interesting server responses.
```bash
gobuster dir -u target-name.com:8000 -w [wordlist]
```

### Kiterunner
Kiterunner, developed by Assetnote, is currently the best tool for discovering API endpoints and resources. Unlike traditional directory brute force tools, Kiterunner:
- Uses all HTTP request methods common with APIs (GET, POST, PUT, DELETE)
- Mimics common API path structures
- Provides more comprehensive endpoint discovery

## Passive Reconnaissance

### Google Dorking

| Google Dorking Query | Expected Results |
|---------------------|------------------|
| inurl:"/wp-json/wp/v2/users" | Finds all publicly available WordPress API user directories |
| intitle:"index.of" intext:"api.txt" | Finds publicly available API key files |
| inurl:"/api/v1" intext:"index of /" | Finds potentially interesting API directories |
| ext:php inurl:"api.php?action=" | Finds sites with XenAPI SQL injection vulnerability |
| intitle:"index of" api_key OR "api key" OR apiKey -pool | Lists potentially exposed API keys |

### Git Dorking
GitHub searches can reveal:
- API capabilities
- Documentation
- Secrets (API keys, passwords, tokens)
- Potential attack vectors

### Shodan.io
- Performs regular scans available to all users
- Start with generic search terms
- Can reveal exposed API endpoints and services

### Wayback Machine
- Internet Archive maintaining website snapshots
- Useful for discovering historical API endpoints
- Can reveal deprecated but still functional endpoints


## Endpoint Analysis

### Excessive Data Exposure
Occurs when APIs return complete data objects instead of necessary fields:
- Look for large response sizes
- Check for sensitive data in responses
- Monitor filtering implementation

### Reverse Engineering API

#### Building Postman Collections
Methods:
1. Manual request construction
   - Precise control
   - Clean collection
   
2. Proxy traffic capture
   - Faster collection building
   - Requires cleanup of irrelevant requests

## Authentication Attacks

### API Token Attacks

#### Token Analysis
Tokens, when implemented correctly, can effectively authenticate and authorize users. However, implementation flaws can create serious vulnerabilities.

Using Burp Sequencer:
- Send thousands of requests to the provider
- Analyze received tokens
- Identify weak token generation patterns
- Look for predictable elements

### JWT Attacks

JWT vulnerabilities arise from misconfiguration and weak implementation. JWTs consist of three base64-encoded parts:
- Header
- Payload
- Signature

Tools:
- [jwt.io](https://jwt.io) - Free web JWT debugger

#### Automating JWT Attacks with jwt_tool

Scan for common JWT vulnerabilities:
```bash
jwt_tool -t http://target-name.com/ -rh "Authorization: Bearer JWT_Token" -M pb
```

##### The None Attack
Steps:
1. Set algorithm to "none"
2. Remove signature (everything after the last period)
3. Send modified JWT to provider
4. Check for unauthorized access

##### Algorithm Switch Attack
Testing process:
1. Send JWT without signature (leave last period)
Example:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJoYWNrYXBpcy5pbyIsImV4cCI6IDE1ODM2Mzc0ODgsInVzZ XJuYW1lIjoiU2N1dHRsZXBoMXNoIiwic3VwZXJhZG1pbiI6dHJ1ZX0.
```

2. Alter algorithm header to "none":
```bash
jwt_tool eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ1c2VyYWFhQGVtYWlsLmNvbSIsImlhdCI6MTY1ODg1NTc0MCwiZXhwIjoxNjU4OTQyMTQwfQ._EcnSozcUnL5y9SFOgOVBMabx_UAr6Kg0Zym-LH_zyjReHrxU_ASrrR6OysLa6k7wpoBxN9vauhkYNHepOcrlA -X a
```

##### JWT Crack Attack
Attempt to crack the JWT signature secret:

1. Generate password list:
```bash
crunch 5 5 -o crAPIpw.txt
```

2. Perform crack attack:
```bash
jwt_tool TOKEN -C -d /wordlist.txt
```
- `-C`: Hash crack attack
- `-d`: Specify dictionary/wordlist

## Classic Authentication Attacks

These attacks target traditional authentication mechanisms:

### Password Brute-Force Attack
Example using wfuzz:
```bash
wfuzz -d "{'email':'test3@example.com', 'password':'FUZZ'}" \
      -H "Content-Type: application/json" \
      -w /usr/share/wordlists/rockyou.txt \
      -u http://127.0.0.1:8888/identify/api/auth/login \
      --hc 500
```

### Password Spraying
Evade security controls by:
- Combining long user list with short password list
- Staying under lockout thresholds
- Using common passwords across multiple accounts

## Authorization Attacks

### Broken Function Level Authorization (BFLA)
BFLA focuses on unauthorized actions rather than unauthorized access:
- Lateral actions: Requests performing actions of same-role users
- Escalated actions: Requests performing administrative-level actions

### Broken Object Level Authorization (BOLA)
Requirements for BOLA exploitation:
1. Resource ID: Unique identifier for resources
2. Request accessing resources
3. Missing/flawed access controls

## Improper Asset Management

Focus on discovering:
- Unsupported API versions
- Non-production environments
- Testing environments
- Development endpoints

Test discovered endpoints for:
- Weaker security controls
- Missing authentication
- Vulnerable configurations

## Mass Assignment Attacks

Similar to Improper Asset Management testing:
- Find unsupported/non-production versions
- Test for weaker security controls
- Exploit version differences

## SSRF Exploitation

Server-Side Request Forgery occurs when applications retrieve remote resources without proper input validation.

### Types of SSRF

#### In-Band SSRF
- Server returns resources specified by user
- Direct feedback of requested resource

#### Blind SSRF
- Server makes request but doesn't return data
- Requires alternative verification methods

### SSRF Requirements
- Full URL in POST body/parameters
- URL paths in POST body/parameters
- Headers containing URLs (e.g., Referer)
- User input leading to server resource retrieval


