# Writeup Editing Guidelines

This document outlines the standard editing process for CTF writeups in this blog repository.

## Purpose

These guidelines ensure all writeups maintain consistent quality, professionalism, and completeness before publication.

## Editing Checklist

### 1. Fill Missing Sections

#### TL;DR Section
- Must contain a comprehensive one-paragraph summary
- Should explain the complete attack chain from initial access to root
- Include: vulnerabilities exploited → key techniques used → privilege escalation path
- Example format: "[Box name] is a [OS] [domain type] box that demonstrates [main concepts]. The attack chain involves [initial foothold method], [lateral movement technique], and [privilege escalation method]."

#### Key Vulnerabilities Section
- Replace placeholder text with actual vulnerabilities discovered
- List 3-5 specific vulnerabilities in order of exploitation
- Use proper vulnerability names (e.g., "AS-REP Roasting", "CVE-XXXX-XXXXX", "Misconfigured permissions")

#### References Section
- Add relevant technical documentation for all tools and techniques used
- Include links to:
  - Tool GitHub repositories
  - HackTricks articles
  - CVE databases (if applicable)
  - Technique documentation
  - PoC repositories
- Format: `- [Description - Source](URL)`

### 2. Grammar and Language Check

- Fix grammar errors and typos
- Ensure proper capitalization (e.g., "BloodHound", "Active Directory")
- Use professional, clear language
- Fix informal phrases (e.g., "Seems like" → "It appears", "its" → "it's")
- Improve sentence structure for clarity
- Use proper punctuation

### 3. Professional Refinement

- Maintain consistent tone throughout
- Ensure technical accuracy
- Use active voice where appropriate
- Keep explanations concise and clear
- Format code blocks consistently
- Ensure proper Markdown formatting

### 4. Technical Content Standards

- Update placeholder values (e.g., `*TARGET_IP*` → actual IP)
- Ensure all commands are complete and accurate
- Verify all file paths and references
- Check that all screenshots are referenced correctly
- Maintain logical flow of the exploitation process

## Critical Rule

**DO NOT CHANGE:**
- The logic of the original text
- The meaning or conclusions
- The technical approach or methodology
- The exploitation steps
- Command outputs or results

**ONLY REFINE:**
- Grammar and language
- Professional presentation
- Missing standardized sections
- Clarity and readability

## Process

When editing a writeup:
1. Read through the entire writeup to understand the attack chain
2. Identify missing or incomplete sections
3. Apply grammar and clarity improvements
4. Fill in missing sections based on the content
5. Add relevant references for tools and techniques used
6. Verify all technical details remain unchanged
7. Commit with descriptive message

## File Location

All writeups are located in: `_writeups/[BoxName].md`

## Example References by Category

### Active Directory
- [Active Directory Methodology - HackTricks](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology)
- [BloodHound - GitHub](https://github.com/BloodHoundAD/BloodHound)

### Credential Attacks
- [AS-REP Roasting - HackTricks](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/asreproast)
- [Kerberoasting - HackTricks](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/kerberoast)
- [Mimikatz & LSASS Dumping - HackTricks](https://book.hacktricks.xyz/windows-hardening/stealing-credentials/credentials-mimikatz)

### Tools
- [Impacket Toolkit - GitHub](https://github.com/fortra/impacket)
- [pypykatz - GitHub](https://github.com/skelsec/pypykatz)
- [Evil-WinRM - GitHub](https://github.com/Hackplayers/evil-winrm)

### Linux Privilege Escalation
- [Linux Privilege Escalation - HackTricks](https://book.hacktricks.xyz/linux-hardening/privilege-escalation)
- [GTFOBins](https://gtfobins.github.io/)

### Web Vulnerabilities
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [SQL Injection - HackTricks](https://book.hacktricks.xyz/pentesting-web/sql-injection)

---

**Last Updated:** January 23, 2026
**Maintainer:** YuvalMil