# Writeup Creation Instructions

This document provides detailed instructions for converting Obsidian notes into professional writeups for the Rootinator blog.

---

## Overview

Writeups should follow the established format demonstrated in `_writeups/Blackfield.md` and use the templates provided in `assets/writeups/templates/`. The goal is to transform raw notes into polished, professional content while maintaining technical accuracy and readability.

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
│   │   ├── Pasted image 20251021170110.png
│   │   ├── Pasted image 20251021170146.png
│   │   └── ...
│   └── writeups/
│       ├── icons/
│       │   ├── Blackfield.png
│       │   └── Flight.png
│       └── templates/
│           ├── Writeup Template Linux.md
│           └── Writeup Template Windows.md
└── obsidian-writeups/
    └── Attachments/
        └── (original Obsidian screenshots)
```

---

## Image Handling

### Understanding the Image Structure

**Obsidian Format** (in source notes):
```markdown
![[Pasted image 20251021170110.png]]
```

**Jekyll/Website Format** (in _writeups):
```markdown
![](../assets/images/Pasted image 20251021170110.png)
```

### Image Path Conversion

1. **Identify the timestamp ID** in Obsidian format: `![[Pasted image YYYYMMDDHHMMSS.png]]`
2. **Convert to relative path**: `../assets/images/Pasted image YYYYMMDDHHMMSS.png`
3. **URL encode spaces**: Spaces become `%20` in URLs (but not in markdown path)

### Image Location

- **Source**: `obsidian-writeups/Attachments/Pasted image TIMESTAMP.png`
- **Destination**: `assets/images/Pasted image TIMESTAMP.png`
- **Reference in _writeups**: `../assets/images/Pasted image TIMESTAMP.png`

**Important**: Images should already exist in `assets/images/` with the same timestamp as Obsidian. If not, they need to be copied from `obsidian-writeups/Attachments/` first.

### Example Conversion

**Obsidian note:**
```markdown
The scan reveals:
![[Pasted image 20251021170110.png]]
```

**Converted writeup:**
```markdown
The scan reveals:

![](../assets/images/Pasted image 20251021170110.png)
```

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

### 2. Machine Information Table (Optional)

For boxes where you want to display structured info:

```markdown
## Machine Information

| Property       | Value      |
| -------------- | ---------- |
| **Name**       | Flight     |
| **OS**         | Windows    |
| **Difficulty** | Hard       |
| **IP**         | 10.10.11.x |
```

### 3. Summary Section

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
9. **Tools Used** (table - optional)
10. **References**
11. **Timeline** (Mermaid diagram - optional)
12. **Footer** (Pwned date + ratings)

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
- Show command output when relevant
- Use `# Commands` or `# Output` to separate

### 7. Tables

Use tables for structured data:

**Port/Service Information:**
```markdown
| Port | Service  | TCP/UDP |
| ---- | -------- | ------- |
| 80   | HTTP     | TCP     |
| 443  | HTTPS    | TCP     |
```

**SMB Shares:**
```markdown
| Share    | Permissions | Description         |
| -------- | ----------- | ------------------- |
| Shared   | READ, WRITE | Custom share        |
| Users    | READ        | Standard users dir  |
```

---

## Conversion Workflow

### Step-by-Step Process

1. **Read Source Material**
   - Read Obsidian notes completely
   - Understand the full attack chain
   - Identify all vulnerabilities
   - Note all image references

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
   - Convert nmap output to table format
   - Add "Key findings" bullets
   - Include all service-specific enumeration

6. **Document Exploitation**
   - Use info dividers for vulnerability explanations
   - Show commands with proper formatting
   - Include relevant output
   - Use warning dividers for exploitation paths

7. **Handle Images**
   - Find image timestamp in Obsidian notes
   - Convert to relative path: `../assets/images/Pasted image TIMESTAMP.png`
   - Verify image exists in `assets/images/`

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
    - Check all image paths
    - Verify code blocks have languages
    - Validate YAML frontmatter
    - Ensure dividers are properly formatted

---

## Quality Checklist

- [ ] Template used as base (Windows or Linux)
- [ ] Frontmatter complete and valid YAML
- [ ] CSS stylesheet link included
- [ ] TL;DR is 2-3 sentences max
- [ ] Key vulnerabilities listed (3-5 items)
- [ ] Sections follow template order
- [ ] Nmap results in table format
- [ ] Code blocks have language specified
- [ ] Images use correct relative paths (`../assets/images/...`)
- [ ] Images verified to exist in assets/images/
- [ ] Custom dividers used appropriately
- [ ] Vulnerability explanations in dividers
- [ ] Commands show input and relevant output
- [ ] Flags documented with commands
- [ ] References section complete
- [ ] Footer includes date and ratings
- [ ] No spelling/grammar errors
- [ ] Technical accuracy verified

---

## Common Mistakes to Avoid

### ❌ Don't:
- Create new templates - use existing ones
- Use raw GitHub URLs for images
- Skip the CSS stylesheet link
- Leave image references as `![[Pasted image...]]`
- Use numbered headers
- Include excessive command output
- Skip TL;DR or key vulnerabilities
- Forget to specify code block languages

### ✅ Do:
- Start from official templates
- Use relative paths: `../assets/images/...`
- Include custom dividers for visual appeal
- Convert Obsidian image syntax properly
- Use markdown headers (`##`, `###`)
- Show only relevant output
- Keep TL;DR concise
- Specify language for every code block

---

## Image Troubleshooting

### Issue: Image not displaying

**Check:**
1. Does image exist in `assets/images/` with exact timestamp?
2. Is path format correct: `../assets/images/Pasted image TIMESTAMP.png`?
3. Is the markdown syntax correct: `![](path)` not `![[path]]`?

**Solution:**
If image is missing from `assets/images/`, copy from `obsidian-writeups/Attachments/` first.

### Issue: Image path has special characters

**Solution:**
Spaces in filenames are fine in the path itself. URL encoding (`%20`) is only needed in actual URLs, not in markdown relative paths.

---

## References

**Example Writeups:**
- `_writeups/Blackfield.md` - Complete example with all features
- `_writeups/DarkZero.md` - Another reference

**Templates:**
- `assets/writeups/templates/Writeup Template Windows.md`
- `assets/writeups/templates/Writeup Template Linux.md`

---

## Notes for AI Assistant

When converting writeups:

1. **Always use existing templates** from `assets/writeups/templates/`
2. **Read source Obsidian notes** for accuracy
3. **Match Blackfield.md style** for consistency
4. **Convert image syntax**: `![[image]]` → `![](../assets/images/image)`
5. **Preserve all commands** exactly as in notes
6. **Use dividers generously** for visual hierarchy
7. **Explain techniques** with info dividers
8. **Keep TL;DR tight** - 2-3 sentences max
9. **Verify image timestamps** match between Obsidian and assets/images

---

**Last Updated:** January 24, 2026
