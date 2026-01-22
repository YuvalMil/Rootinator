---
layout: post
title: "Example CTF Challenge"
date: 2026-01-22
categories: [ctf, web]
tags: [sql-injection, authentication-bypass]
---

## Challenge Overview

This is an example writeup template for CTF challenges.

**Challenge Name:** Example Challenge  
**Category:** Web Security  
**Difficulty:** Medium  
**Points:** 500

## Initial Reconnaissance

Describe your initial analysis of the challenge...

## Vulnerability Discovery

Explain the vulnerability you found...

```python
# Example exploit code
import requests

payload = "' OR 1=1--"
response = requests.post(url, data={'username': payload})
```

## Exploitation

Detail the exploitation steps...

## Flag

`flag{example_flag_here}`

## Lessons Learned

- Key takeaway 1
- Key takeaway 2