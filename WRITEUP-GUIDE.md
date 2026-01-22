# Adding Writeups to Rootinator

This guide explains how to add new CTF writeups to your blog.

## Quick Start

1. **Prepare your PDF writeup** (any PDF viewer/creator)
2. **Get the box icon URL** (from HTB/THM/etc.)
3. **Create a metadata file** in `_writeups/`
4. **Upload your PDF** to `assets/writeups/`
5. **Commit and push** to GitHub

## Step-by-Step Instructions

### 1. Create Writeup Metadata File

Create a new file in `_writeups/` directory:

**Naming convention:** `platform-boxname.md`

Examples:
- `_writeups/hackthebox-usage.md`
- `_writeups/tryhackme-windows-forensics.md`
- `_writeups/picoctf-buffer-overflow-3.md`

### 2. Add Front Matter

```yaml
---
layout: writeup
title: "Box Name"
platform: "HackTheBox"  # or "TryHackMe", "PicoCTF", "Pwnable", etc.
difficulty: "Medium"     # Easy, Medium, Hard, or Insane
box_icon: "/assets/writeups/icons/boxname.png"  # or direct URL
pdf_file: "/assets/writeups/hackthebox-boxname.pdf"
date: 2026-01-22
tags: [web, sql-injection, privilege-escalation]
---

Optional description here (won't show if PDF is used).
```

### 3. Upload Files

**PDF Writeup:**
- Save your PDF to: `assets/writeups/your-writeup.pdf`

**Box Icon (optional):**
- Save icon to: `assets/writeups/icons/boxname.png`
- Or use direct URL from platform

**HTB Icon URLs:**
```
https://labs.hackthebox.com/storage/avatars/[AVATAR_ID].png
```
Find on the box page source.

**THM Icon URLs:**
```
https://tryhackme-images.s3.amazonaws.com/room-icons/[ICON_ID].png
```
Find on the room page source.

### 4. Difficulty Levels

Supported difficulties with color coding:
- **Easy** - Green badge
- **Medium** - Orange badge
- **Hard** - Red badge
- **Insane** - Grey badge

### 5. Platform Names

Supported platforms (case-insensitive):
- HackTheBox
- TryHackMe
- PicoCTF
- Pwnable
- (Add custom platforms - they'll use default styling)

### 6. Tags

Common tags for CTF writeups:
- **Categories:** web, pwn, crypto, forensics, reversing, osint, misc
- **Techniques:** sql-injection, xss, buffer-overflow, rop, privilege-escalation
- **OS:** linux, windows, active-directory
- **Tools:** nmap, burp-suite, ghidra, wireshark

## Example Writeup

### File: `_writeups/hackthebox-usage.md`

```yaml
---
layout: writeup
title: "Usage"
platform: "HackTheBox"
difficulty: "Medium"
box_icon: "https://labs.hackthebox.com/storage/avatars/8e58013d74f95bbb1f24ae01d3e8ed88.png"
pdf_file: "/assets/writeups/hackthebox-usage.pdf"
date: 2026-01-15
tags: [web, sql-injection, laravel, sudo]
---
```

### File: `assets/writeups/hackthebox-usage.pdf`
(Your actual PDF writeup)

## Local Testing

Test your writeup locally before pushing:

```bash
bundle exec jekyll serve
```

Visit:
- Gallery: `http://localhost:4000/writeups/`
- Individual: `http://localhost:4000/writeups/hackthebox-usage/`

## Deployment

Once satisfied:

```bash
git add .
git commit -m "Add [BoxName] writeup"
git push origin main
```

Cloudflare Pages will automatically rebuild and deploy your site in 1-2 minutes.

## Tips

### Creating Good Writeup PDFs

1. **Use Markdown → PDF tools:**
   - Obsidian with PDF export
   - Typora
   - Pandoc: `pandoc writeup.md -o writeup.pdf`

2. **Include in your writeup:**
   - Box overview and stats
   - Enumeration steps
   - Exploitation methodology
   - Screenshots of key steps
   - Code/scripts used
   - Lessons learned

3. **Dark theme PDFs:**
   Use dark backgrounds to match your site theme!

### Organizing Multiple Writeups

Folder structure as your collection grows:

```
assets/writeups/
├── hackthebox/
│   ├── usage.pdf
│   └── usage-icon.png
├── tryhackme/
│   ├── windows-forensics.pdf
│   └── windows-forensics-icon.png
└── picoctf/
    └── buffer-overflow.pdf
```

Update paths in front matter accordingly.

## Troubleshooting

**PDF not displaying?**
- Check file path is correct
- Ensure PDF is valid (open locally first)
- Try different browser
- Check browser console for errors

**Icon not showing?**
- Verify URL is accessible
- Check file extension (.png, .jpg)
- Use absolute URLs for external images

**Card not appearing in gallery?**
- Check front matter syntax (YAML)
- Ensure file is in `_writeups/` directory
- Verify layout is set to `writeup`

## Questions?

Check Jekyll documentation or create an issue if you encounter problems!