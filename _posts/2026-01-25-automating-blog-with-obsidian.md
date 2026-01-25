---
layout: post
title: "How I Automated My Blog to Update from Obsidian Notes"
date: 2026-01-25 10:51:00 +0200
categories: [tutorial, automation, obsidian]
tags: [obsidian, github-actions, jekyll, automation, workflow]
---

## Introduction

In this post, I'll explain how I built an automated workflow that syncs my Obsidian vault directly to my Jekyll blog, eliminating the manual copy-paste process and ensuring my writeups are always up-to-date.

## The Problem

Writing technical writeups in Obsidian is great for:
- Quick note-taking during CTF challenges
- Linking related concepts
- Embedding images easily
- Local version control

However, publishing to a Jekyll blog traditionally meant:
1. Copying markdown files manually
2. Fixing image links (Obsidian uses `![[image.png]]` format)
3. Adjusting frontmatter
4. Uploading images to the correct directory
5. Committing and pushing changes

This manual process was tedious and error-prone.

## The Solution

### Architecture Overview

```
Obsidian Vault (obsidian-notes branch)
    ↓
GitHub Actions Workflow
    ↓
1. Detect new/modified writeups
2. Convert Obsidian image syntax to Jekyll format
3. Copy images to assets directory
4. Update markdown files
5. Commit changes to main branch
    ↓
Jekyll Build & Deploy
```

### Key Components

#### 1. Two-Branch Strategy

- **`obsidian-notes` branch**: Contains my raw Obsidian vault
- **`main` branch**: Contains the processed Jekyll blog

#### 2. GitHub Actions Workflow

[TODO: Add workflow details]

#### 3. Image Conversion Script

[TODO: Add script explanation]

### Step-by-Step Implementation

#### Step 1: Repository Structure

[TODO: Explain repository organization]

#### Step 2: GitHub Actions Configuration

[TODO: Add workflow YAML]

#### Step 3: Image Processing

[TODO: Explain image handling]

#### Step 4: Frontmatter Conversion

[TODO: Explain metadata handling]

## Benefits

1. **Zero Manual Work**: Write in Obsidian, commit, and the blog updates automatically
2. **Consistent Formatting**: Automated conversion ensures all posts follow the same structure
3. **Image Management**: Images are automatically copied and links updated
4. **Version Control**: Full history of both raw notes and published content
5. **Fast Workflow**: From note completion to live blog post in minutes

## Challenges Faced

[TODO: Add challenges and solutions]

## Future Improvements

- [ ] Add support for Obsidian callouts
- [ ] Implement tag synchronization
- [ ] Add automated SEO optimization
- [ ] Create a dashboard for writeup statistics

## Conclusion

[TODO: Add conclusion]

---

**Tech Stack Used:**
- Obsidian
- GitHub Actions
- Jekyll
- Python (for image processing)
- GitHub Pages

**Resources:**
- [GitHub Repository](https://github.com/YuvalMil/Rootinator)
- [Obsidian Documentation](https://help.obsidian.md/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
