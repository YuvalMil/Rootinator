# Obsidian → Jekyll Workflow Setup

This guide will help you set up Obsidian to write and publish CTF writeups directly to your blog.

## Prerequisites

- Obsidian installed on your computer
- Git installed and configured
- Your GitHub repository cloned locally

## Step 1: Set Up Obsidian Vault

### Option A: Use Your Repo as Vault (Recommended)

1. Open Obsidian
2. Click "Open folder as vault"
3. Select your cloned `rootinator` repository folder
4. Obsidian will now use your repo as the vault

### Option B: Link Existing Vault

If you have an existing Obsidian vault:
1. Copy your vault contents to the repo
2. Or use symlinks to connect folders

## Step 2: Install Obsidian Git Plugin

1. In Obsidian: **Settings** → **Community plugins** → **Turn off Safe Mode**
2. Click **Browse** → Search for **"Obsidian Git"**
3. Install and Enable the plugin
4. Configure:
   - **Settings** → **Obsidian Git**
   - Set **Vault backup interval**: 10 minutes (or your preference)
   - Enable **Auto pull on startup**
   - Enable **Auto push**
   - Set **Commit message**: `vault backup: {{date}}`

## Step 3: Folder Structure

Your Obsidian vault should mirror the Jekyll structure:

```
rootinator/
├── _writeups/              # Your writeup markdown files
│   ├── hackthebox-usage.md
│   └── tryhackme-windows.md
├── assets/
│   └── images/             # Screenshots and images
│       ├── usage/
│       └── windows/
├── templates/              # Obsidian templates (optional folder)
│   └── writeup-template.md
└── _config.yml
```

## Step 4: Create Writeup Template

### Create Template File

1. Create a folder: `templates/`
2. Create file: `templates/writeup-template.md`

### Template Content

```markdown
---
layout: writeup
title: "{{title}}"
platform: "HackTheBox"
difficulty: "Medium"
box_icon: "/assets/images/{{title}}-icon.png"
date: {{date:YYYY-MM-DD}}
tags: [web, linux]
---

## Box Information

- **Name:** {{title}}
- **Difficulty:** Medium
- **OS:** Linux
- **IP:** 10.10.11.x

## Enumeration

### Nmap Scan

```bash
nmap -sC -sV -oA nmap/{{title}} 10.10.11.x
```

**Results:**
```
PORT   STATE SERVICE VERSION
```

### Web Enumeration

## Initial Access

### Vulnerability Discovery

### Exploitation

```python
# Exploit code
```

## Privilege Escalation

### User Flag

```bash
cat /home/user/user.txt
```

### Root Escalation

### Root Flag

```bash
cat /root/root.txt
```

## Lessons Learned

- Key takeaway 1
- Key takeaway 2
- Key takeaway 3

## Tools Used

- nmap
- burpsuite
- linpeas
```

### Enable Templates in Obsidian

1. **Settings** → **Core plugins** → Enable **Templates**
2. **Settings** → **Templates** → Set **Template folder location**: `templates`
3. To use: In any note, type `/template` or use hotkey

## Step 5: Writing Workflow

### Create New Writeup

1. In `_writeups/` folder, create new note
2. Name it: `platform-boxname.md` (e.g., `hackthebox-usage.md`)
3. Insert template (Cmd/Ctrl + P → "Templates: Insert template")
4. Fill in the front matter
5. Write your writeup in Markdown

### Add Images

#### Method 1: Drag and Drop
1. Drag image into Obsidian
2. It will be saved to `assets/images/`
3. Reference: `![alt text](/assets/images/screenshot.png)`

#### Method 2: Paste from Clipboard
1. Take screenshot (Cmd/Ctrl + Shift + 4 or Snipping Tool)
2. Paste into Obsidian (Cmd/Ctrl + V)
3. Image auto-saves to `assets/images/`

#### Configure Image Settings
1. **Settings** → **Files & Links**
2. **Default location for new attachments**: `assets/images`
3. **New link format**: `Absolute path in vault`

### Markdown Tips for CTF Writeups

**Code blocks with syntax highlighting:**
```python
import requests
# Your code
```

**Callouts (Info boxes):**
```markdown
> [!info] Note
> This is important information

> [!warning] Warning  
> Be careful here

> [!tip] Tip
> Pro tip for solving this
```

**Tables:**
```markdown
| Port | Service | Version |
|------|---------|----------|
| 22   | SSH     | OpenSSH 8.2 |
| 80   | HTTP    | Apache 2.4 |
```

## Step 6: Publishing Workflow

### Automatic (Recommended)

With Obsidian Git plugin configured:
1. Write your writeup
2. Save (Cmd/Ctrl + S)
3. Plugin auto-commits and pushes every 10 minutes
4. Cloudflare Pages detects push and rebuilds
5. Your writeup goes live in 1-2 minutes!

### Manual

If you prefer manual control:
1. Open Command Palette (Cmd/Ctrl + P)
2. Type "Git: Commit all changes"
3. Type "Git: Push"
4. Or use terminal: `git add . && git commit -m "Add writeup" && git push`

## Step 7: Front Matter Quick Reference

### Required Fields
```yaml
---
layout: writeup           # Always "writeup"
title: "Box Name"         # Name of the box/challenge
platform: "HackTheBox"   # HackTheBox, TryHackMe, PicoCTF, etc.
difficulty: "Medium"     # Easy, Medium, Hard, Insane
date: 2026-01-22         # Publication date
---
```

### Optional Fields
```yaml
box_icon: "/assets/images/icon.png"  # Box icon image
tags: [web, linux, privesc]           # Categories/tags
```

### Platform Values
- `HackTheBox`
- `TryHackMe`
- `PicoCTF`
- `Pwnable`
- Any custom platform name

### Difficulty Values
- `Easy` (green badge)
- `Medium` (orange badge)
- `Hard` (red badge)
- `Insane` (silver badge)

## Tips & Best Practices

### Organization

**Organize images by box:**
```
assets/images/
├── usage/
│   ├── nmap-scan.png
│   ├── web-vuln.png
│   └── root-flag.png
└── windows-forensics/
    ├── registry.png
    └── timeline.png
```

**Use descriptive filenames:**
- ✅ `usage-nmap-scan.png`
- ❌ `screenshot1.png`

### Writing Style

- Use headings (##, ###) to organize sections
- Code blocks for commands and output
- Screenshots for visual steps
- Explain your methodology, not just the solution

### Before Publishing

- [ ] Check all images display correctly
- [ ] Verify front matter is complete
- [ ] Test code blocks format properly
- [ ] Proofread for typos
- [ ] Remove any sensitive information

## Troubleshooting

### Images Not Displaying

- Check image path starts with `/assets/images/`
- Verify image file exists in repo
- Use absolute paths, not relative: `/assets/images/pic.png` not `../images/pic.png`

### Git Push Fails

- Check internet connection
- Verify Git credentials
- Try manual push: `git push origin main`

### Site Not Updating

- Check Cloudflare Pages deployments
- Verify commit actually pushed to GitHub
- Wait 2-3 minutes for rebuild

## Advanced: Obsidian Plugins for CTF

### Recommended Plugins

1. **Obsidian Git** - Auto sync with GitHub
2. **Image Auto Upload** - Auto-compress and upload images
3. **Advanced Tables** - Better table editing
4. **Paste URL into selection** - Quick link formatting
5. **Tag Wrangler** - Manage tags easily

## Example Complete Writeup

See `_writeups/example-complete-writeup.md` for a full example with all formatting features.

---

You're all set! Start writing your first writeup in Obsidian and watch it auto-publish to your blog! 🚀