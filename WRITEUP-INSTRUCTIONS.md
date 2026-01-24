# Writeup Creation Instructions

This document provides detailed instructions for converting Obsidian notes into professional writeups for the Rootinator blog.

---

## Overview

Writeups should follow the established format demonstrated in `_writeups/Blackfield.md` and use the templates provided in `assets/writeups/templates/`. The goal is to transform raw notes into polished, professional content while maintaining technical accuracy and readability.

---

## Automated Workflow

### GitHub Actions Automation

**The repository includes an automated workflow** that handles image conversion from Obsidian to Jekyll format.

**Workflow file:** `.github/workflows/convert-writeup-images.yml`

**What it does:**
1. Monitors for changes to files in `_writeups/*.md`
2. Detects Obsidian-style image syntax: `![[image.png]]`
3. Copies images from `obsidian-notes` branch (`obsidian-writeups/Assets/Attachments/`) to `main` branch (`assets/images/YYYY-MM-DD-MachineName/`)
4. Converts image syntax to Jekyll format: `![image.png](/assets/images/YYYY-MM-DD-MachineName/image.png)`
5. Commits changes automatically

**How to use:**
1. Write your writeup in `_writeups/` using Obsidian-style image links: `![[Pasted image 20251021170110.png]]`
2. Push to main branch
3. The workflow automatically:
   - Copies all referenced images from obsidian-notes branch
   - Converts the image syntax to proper Jekyll format
   - Creates a directory: `assets/images/YYYY-MM-DD-MachineName/`
   - Commits the changes with message: "Auto-convert Obsidian images to Jekyll format [skip ci]"

**Manual trigger:** You can also trigger the workflow manually from the Actions tab on GitHub.

**Requirements:**
- Images must exist in the `obsidian-notes` branch at `obsidian-writeups/Assets/Attachments/`
- Writeup must have a valid `date:` field in the frontmatter
- Image filenames must match exactly between Obsidian notes and the branch

---

## Templates

Use the official templates located in `assets/writeups/templates/`:

- **Windows boxes**: `assets/writeups/templates/Writeup Template Windows.md`
- **Linux boxes**: `assets/writeups/templates/Writeup Template Linux.md`

These templates include:
- Proper frontmatter structure
- CSS stylesheet link
- Section organization
- Custom dividers
- Code block formatting
- Table structures
- Mermaid diagram placeholders

**Do not create new templates** - always use the existing ones as your base.

---

## File Structure

### Naming Convention
```
_writeups/{MachineName}.md
```

**Examples:**
- `_writeups/Blackfield.md` (capital first letter)
- `_writeups/Flight.md`
- `_writeups/DarkZero.md`

### Directory Structure
```
Rootinator/
├── _writeups/
│   ├── Blackfield.md
│   ├── Flight.md
│   └── ...
├── assets/
│   ├── images/
│   │   ├── 2025-10-21-Flight/
│   │   │   ├── Pasted image 20251021170110.png
│   │   │   ├── Pasted image 20251021170146.png
│   │   │   └── ...
│   │   └── 2026-01-23-Blackfield/
│   │       └── ...
│   └── writeups/
│       ├── icons/
│       │   ├── Blackfield.png
│       │   └── Flight.png
│       └── templates/
│           ├── Writeup Template Linux.md
│           └── Writeup Template Windows.md
└── .github/
    └── workflows/
        └── convert-writeup-images.yml
```

---

## Simulating Terminal Output

### When Screenshots Are Missing

**Important:** When the original Obsidian notes are missing screenshots for commands, **simulate realistic terminal output** based on:
- The command being executed
- The context of the attack
- Expected behavior of the tool/command
- Standard output format from official HTB writeups

### How to Simulate Output

**Example 1: Nmap scan output**
```bash
❯ nmap -vv -T5 -p- 10.129.229.17

Starting Nmap 7.94 ( https://nmap.org )
Scanning 10.129.229.17 [65535 ports]
Discovered open port 445/tcp on 10.129.229.17
Discovered open port 53/tcp on 10.129.229.17
Discovered open port 88/tcp on 10.129.229.17
Completed SYN Stealth Scan at 14:25, 42.31s elapsed (65535 total ports)
Nmap scan report for 10.129.229.17
PORT      STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
```

**Example 2: SMB enumeration**
```bash
❯ nxc smb 10.129.229.17 -u 'Guest' -p '' --shares
SMB         10.129.229.17   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64
SMB         10.129.229.17   445    DC01             [+] BLACKFIELD.local\Guest: 
SMB         10.129.229.17   445    DC01             [*] Enumerated shares
SMB         10.129.229.17   445    DC01             Share           Permissions     Remark
SMB         10.129.229.17   445    DC01             -----           -----------     ------
SMB         10.129.229.17   445    DC01             ADMIN$                          Remote Admin
SMB         10.129.229.17   445    DC01             C$                              Default share
SMB         10.129.229.17   445    DC01             profiles$       READ            
```

**Example 3: Hash cracking**
```bash
❯ john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (krb5asrep, Kerberos 5 AS-REP etype 23 [MD4 HMAC-MD5 RC4])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
#00^BlackKnight   ($krb5asrep$support@BLACKFIELD.LOCAL)
1g 0:00:00:08 DONE (2026-01-23 18:51) 0.1234g/s 1234Kp/s 1234Kc/s 1234KC/s
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

### Guidelines for Simulation

1. **Use realistic hostnames, IPs, and usernames** from the box
2. **Match the tool's actual output format** (nmap, nxc, john, etc.)
3. **Show relevant information** that would appear in real output
4. **Use appropriate timestamps and timing** data
5. **Include both commands and output** in the same code block
6. **Add comments** with `#` to separate commands from output when needed
7. **Use the `❯` prompt** for consistency (or `$` for regular user, `#` for root)
8. **Keep it concise** - show only relevant output, truncate with `...` if needed

### When to Simulate vs Use Screenshots

**Use screenshots (`![[image.png]]`) when:**
- Visual confirmation is important (web interfaces, GUIs)
- Output contains complex formatting or colors
- Original screenshot exists in Obsidian notes
- Showing exploit PoC working

**Simulate terminal output when:**
- Screenshot is missing from Obsidian notes
- Command output is standard/predictable
- Terminal text can be easily formatted in markdown
- Output would be too verbose as an image

---

## Image Handling

### Automated Conversion (Recommended)

**Simply use Obsidian-style syntax in your writeups:**
```markdown
![[Pasted image 20251021170110.png]]
```

The GitHub Actions workflow will automatically:
1. Copy the image from `obsidian-notes` branch
2. Place it in `assets/images/YYYY-MM-DD-MachineName/`
3. Update the markdown to Jekyll format: `![Pasted image 20251021170110.png](/assets/images/2025-10-21-Flight/Pasted image 20251021170110.png)`

### Manual Conversion (If Needed)

If the workflow doesn't run or you need to manually convert:

**Obsidian Format** (in source notes):
```markdown
![[Pasted image 20251021170110.png]]
```

**Jekyll/Website Format** (in _writeups):
```markdown
![Pasted image 20251021170110.png](/assets/images/2025-10-21-Flight/Pasted image 20251021170110.png)
```

### Image Location

- **Source**: `obsidian-notes` branch at `obsidian-writeups/Assets/Attachments/Pasted image TIMESTAMP.png`
- **Destination**: `main` branch at `assets/images/YYYY-MM-DD-MachineName/Pasted image TIMESTAMP.png`
- **Reference in writeup**: `/assets/images/YYYY-MM-DD-MachineName/Pasted image TIMESTAMP.png`

---

## Frontmatter Structure

Use the exact frontmatter from the template, filling in the appropriate values:

```yaml
---
layout: writeup
title: "MachineName"
platform: HackTheBox
difficulty: Hard
box_icon: /assets/writeups/icons/MachineName.png
date: YYYY-MM-DD
tags:
  - active-directory
  - privilege-escalation
  - web
os: Windows
ip: 10.129.95.212
---
```

**Required Fields:**
- `layout`: Always `writeup`
- `title`: Machine name (capitalize first letter)
- `platform`: HackTheBox, TryHackMe, VulnHub, etc.
- `difficulty`: Easy, Medium, Hard, Insane
- `date`: Date writeup was completed (YYYY-MM-DD format)
- `os`: Windows or Linux

**Optional:**
- `box_icon`: Path to machine icon (if available)
- `ip`: Target IP address from the box
- `tags`: Relevant techniques/vulnerabilities

---

## Content Structure

### 1. CSS Link (Required)

Always include immediately after frontmatter:

```html
<link rel="stylesheet" href="{{ '/assets/css/obsidian-dividers.css' | relative_url }}">
```

### 2. Summary Section

```markdown
## Summary

<div class="divider divider-info">
    <span class="divider-title">TL;DR</span>
    <span class="divider-content">Brief 2-3 sentence overview of the entire attack chain.</span>
</div>

**Key Vulnerabilities:**
- Vulnerability 1
- Vulnerability 2
- Vulnerability 3
```

### 3. Nmap Scan Format (Standardized)

**Follow the Blackfield format exactly:**

```markdown
### Nmap Scan

**Initial scan:**
```bash
nmap -vv -T5 -p- 10.129.229.17

nmap -vv -T5 -p53,88,135,139,389,445,593,3268,5985 -sC -sV 10.129.229.17
```

**Results:**

| Port | Service  | TCP/UDP |
| ---- | -------- | ------- |
| 53   | DNS      | TCP     |
| 88   | Kerberos | TCP     |
| 135  | RPC      | TCP     |
| 139  | Netbios  | TCP     |
| 389  | LDAP     | TCP     |
| 445  | SMB      | TCP     |
| 593  | HTTP RPC | TCP     |
| 3268 | LDAP     | TCP     |
| 5985 | WinRM    | TCP     |

**Key findings:**
- Finding 1
- Finding 2
```

**Important:** 
- Use two scan commands (full port scan, then targeted scan)
- Table format with Port | Service | TCP/UDP columns
- Keep service names simple and consistent
- Add "Key findings" bullet points
- **If screenshot missing:** Simulate realistic nmap output in code block

### 4. Section Organization

Follow this standard order (from templates):

1. **Summary** (TL;DR + Key Vulnerabilities)
2. **Enumeration**
   - Nmap Scan (with results table)
   - Service-specific enumeration (SMB, Web, LDAP, etc.)
3. **Initial Foothold** / **Initial Access**
   - Vulnerability Discovery
   - Exploitation
4. **User Flag**
5. **Lateral Movement** (if applicable)
6. **Privilege Escalation**
7. **Root Flag**
8. **Post-Exploitation** (optional)
9. **References**
10. **Timeline** (Mermaid diagram - optional)
11. **Footer** (Pwned date + ratings)

### 5. Custom Dividers

**Info Divider** (explanations, technical concepts):
```html
<div class="divider divider-info">
    <span class="divider-title">Concept Name</span>
    <span class="divider-content">Explanation of the concept.</span>
</div>
```

**Warning Divider** (vulnerabilities, exploitation techniques):
```html
<div class="divider divider-warning">
    <span class="divider-title">Vulnerability</span>
    <span class="divider-content">Exploitation details.</span>
</div>
```

**Root Divider** (successful access, flags):
```html
<div class="divider divider-root">
    <span class="divider-title">Root Access</span>
    <span class="divider-content">Successfully escalated privileges to root</span>
</div>
```

### 6. Code Blocks

```markdown
```bash
# Description of command
command --flags arguments
```
```

**Guidelines:**
- Always specify language
- Include comments for complex commands
- **Show simulated output when screenshot is missing**
- Use `# Commands` or `# Output` to separate
- Use `❯` prompt for consistency

### 7. Tables

Use tables for structured data following Blackfield's format:

**Port/Service Information:**
```markdown
| Port | Service  | TCP/UDP |
| ---- | -------- | ------- |
| 80   | HTTP     | TCP     |
| 443  | HTTPS    | TCP     |
```

**SMB Shares:**
```markdown
| Share    | Permissions | Remark                  |
| -------- | ----------- | ----------------------- |
| Shared   | READ, WRITE | Custom share            |
| Users    | READ        | Standard users dir      |
```

---

## Conversion Workflow

### Step-by-Step Process

1. **Read Source Material**
   - Read Obsidian notes completely
   - Understand the full attack chain
   - Identify all vulnerabilities
   - Note all image references
   - **Identify missing screenshots that need simulated output**

2. **Choose Template**
   - Windows: `assets/writeups/templates/Writeup Template Windows.md`
   - Linux: `assets/writeups/templates/Writeup Template Linux.md`

3. **Fill Frontmatter**
   - Extract machine name, IP, date from notes
   - Set difficulty based on platform rating
   - Add relevant tags from attack techniques used

4. **Write Summary**
   - 2-3 sentence TL;DR in divider
   - List 3-5 key vulnerabilities

5. **Process Enumeration**
   - Convert nmap output to **Blackfield-style table format**
   - Use two-command nmap format (full scan + targeted)
   - **Simulate nmap output if screenshot missing**
   - Add "Key findings" bullets
   - Include all service-specific enumeration

6. **Document Exploitation**
   - Use info dividers for vulnerability explanations
   - Show commands with proper formatting
   - **Include simulated output where screenshots are missing**
   - Use warning dividers for exploitation paths

7. **Handle Images and Output**
   - **Leave images in Obsidian format**: `![[Pasted image TIMESTAMP.png]]`
   - The GitHub Actions workflow will automatically convert them
   - **For missing screenshots:** Simulate realistic terminal output
   - Base simulations on the command context and expected tool behavior

8. **Add Flags**
   - Show how flags were obtained
   - Include commands used

9. **Add References**
   - Link to all tools used
   - Reference techniques (HackTricks, MITRE)
   - Credit any PoCs or scripts

10. **Add Timeline** (optional)
    - Create Mermaid diagram showing attack flow

11. **Add Footer**
    - Pwned date
    - Personal difficulty rating (⭐)
    - Fun factor rating (⭐)

12. **Review**
    - Check frontmatter is valid YAML
    - Verify code blocks have languages
    - Ensure dividers are properly formatted
    - Confirm nmap section follows Blackfield format
    - **Verify simulated output looks realistic**

---

## Quality Checklist

- [ ] Template used as base (Windows or Linux)
- [ ] Frontmatter complete and valid YAML
- [ ] CSS stylesheet link included
- [ ] TL;DR is 2-3 sentences max
- [ ] Key vulnerabilities listed (3-5 items)
- [ ] Sections follow template order
- [ ] **Nmap section follows Blackfield format** (two commands + table)
- [ ] Code blocks have language specified
- [ ] **Images left in Obsidian format** `![[image.png]]` for auto-conversion
- [ ] **Missing screenshots replaced with simulated terminal output**
- [ ] Custom dividers used appropriately
- [ ] Vulnerability explanations in dividers
- [ ] Commands show input and relevant output (real or simulated)
- [ ] Flags documented with commands
- [ ] References section complete
- [ ] Footer includes date and ratings
- [ ] No spelling/grammar errors
- [ ] Technical accuracy verified

---

## Common Mistakes to Avoid

### ❌ Don't:
- Create new templates - use existing ones
- Manually convert image paths - let the workflow do it
- Skip the CSS stylesheet link
- Use different nmap format - follow Blackfield exactly
- Use numbered headers
- Include excessive command output
- Skip TL;DR or key vulnerabilities
- Forget to specify code block languages
- Leave commands without output when screenshot is missing
- Use unrealistic or generic simulated output

### ✅ Do:
- Start from official templates
- Leave images in Obsidian format: `![[image.png]]`
- Follow Blackfield's nmap format exactly
- **Simulate realistic terminal output for missing screenshots**
- Include custom dividers for visual appeal
- Use markdown headers (`##`, `###`)
- Show only relevant output (real or simulated)
- Keep TL;DR concise
- Specify language for every code block
- Match actual tool output formats when simulating

---

## Troubleshooting

### Issue: Workflow didn't run

**Check:**
1. Did you push changes to a file in `_writeups/`?
2. Is the file named correctly (`_writeups/MachineName.md`)?
3. Check GitHub Actions tab for workflow status

**Solution:**
Make a small change to the writeup file (add a space) and push again to trigger the workflow.

### Issue: Images not copied

**Check:**
1. Do images exist in `obsidian-notes` branch at `obsidian-writeups/Assets/Attachments/`?
2. Are image filenames exactly the same in the writeup and in the branch?
3. Does the writeup have a valid `date:` field in frontmatter?

**Solution:**
Verify images exist in obsidian-notes branch with correct names. Check workflow logs in GitHub Actions for specific errors.

### Issue: Image syntax not converted

**Check:**
1. Did the workflow complete successfully?
2. Check for a commit from `github-actions[bot]` after your push

**Solution:**
Check the workflow logs in GitHub Actions. The workflow should create a commit with message "Auto-convert Obsidian images to Jekyll format".

---

## References

**Example Writeups:**
- `_writeups/Blackfield.md` - **Primary reference** for format
- `_writeups/Flight.md` - Shows automated workflow in action
- `_writeups/Astronaut.md` - Shows simulated terminal output

**Templates:**
- `assets/writeups/templates/Writeup Template Windows.md`
- `assets/writeups/templates/Writeup Template Linux.md`

**Workflow:**
- `.github/workflows/convert-writeup-images.yml`

**Official HTB Resources:**
- [HTB Writeup Guidelines](https://forum.hackthebox.com/t/writeup-guidelines/24)
- [HTB Business CTF Writeups](https://github.com/hackthebox/business-ctf-2025)

---

## Notes for AI Assistant

When converting writeups:

1. **Always use existing templates** from `assets/writeups/templates/`
2. **Read source Obsidian notes** for accuracy
3. **Match Blackfield.md style** for consistency, especially:
   - Nmap format (two commands + table with Port | Service | TCP/UDP)
   - Table structures
   - Divider usage
4. **Leave images in Obsidian format**: `![[image.png]]` - the workflow handles conversion
5. **Simulate terminal output** when screenshots are missing:
   - Use realistic output based on the command and tool
   - Match standard output formats (nmap, nxc, john, etc.)
   - Include relevant details from the context
   - Use `❯` prompt for consistency
6. **Preserve all commands** exactly as in notes
7. **Use dividers generously** for visual hierarchy
8. **Explain techniques** with info dividers
9. **Keep TL;DR tight** - 2-3 sentences max
10. **Follow Blackfield's nmap format exactly** - this is critical for consistency

---

**Last Updated:** January 24, 2026
