---
layout: post
title: "OSCP+ Certification Review: My Journey from SOC Analyst to Offensive Security"
date: 2026-01-25 13:34:00 +0200
categories: [certification, review, oscp]
tags: [oscp, oscp-plus, offensive-security, certification, penetration-testing, review]
---

## Introduction

In November 2025, I passed the OSCP+ certification on my first attempt after 6 months of intensive preparation. This review covers my complete journey from a SOC analyst with no pentesting experience to earning one of the most respected certifications in offensive security.

If you're considering OSCP+ and want an honest, no-BS review from someone who recently walked this path - this post is for you.

## What is OSCP+?

The Offensive Security Certified Professional Plus (OSCP+) is an advanced penetration testing certification that validates your ability to identify vulnerabilities, exploit systems, and document findings - all under time pressure in a proctored environment.

Unlike multiple-choice certifications, OSCP+ requires you to actually hack machines in a 24-hour hands-on exam, followed by comprehensive report writing.

### Key Differences from Standard OSCP

OSCP+ includes:
- Active Directory attack chains (mandatory AD set in exam)
- Advanced privilege escalation techniques
- More complex multi-stage exploitation scenarios
- Stricter passing requirements

**Important note:** I went straight for OSCP+ without holding the standard OSCP first. This review reflects the OSCP+ experience specifically.

## My Background

Before starting OSCP+ preparation, my background was:

**Professional Experience:**
- 1 year as a SOC Analyst
- 4-5 months of self-study focusing specifically on pentesting and offensive security

**Certifications Held:**
- CompTIA CySA+ (Cybersecurity Analyst)
- Microsoft SC-200 (Security Operations Analyst)

**Offensive Security Experience:**
- Essentially zero practical pentesting experience
- No previous OSCP or OffSec certifications

### Why OSCP+?

I chose to pursue OSCP+ for two main reasons:

1. **Genuine Interest**: Strong passion for offensive security and wanting to transition from defensive (SOC) to offensive roles
2. **Market Value**: OSCP+ is a practical, hands-on certification that holds real weight in the job market - not just another PDF cert

The goal was clear: validate my skills with something that proves I can actually hack, not just memorize theory.

---

## Study Path & Timeline

### Total Preparation Time: 6 Months

**Could I have taken it earlier?** Probably after 4 months, but I wanted to be confident in passing on the first attempt. The exam fee isn't cheap, and neither is my time.

### Time Investment

- **Daily study (months 1-5):** 5-6 hours/day
- **Daily study (month 6, pre-exam):** 8-9 hours/day
- **Employment status:** Full-time study (not working)
- **Total machines completed:** ~80 boxes across HTB and PG

### Lab Time Purchased

**Important:** I did NOT purchase OffSec's official lab access. Instead:

- **HackTheBox:** ~3 months subscription
- **Proving Grounds:** 1 month subscription
- **TryHackMe:** Used extensively for fundamentals

This decision saved me money while still providing excellent preparation. However, I do recommend doing at least 10-15 OffSec-style machines on Proving Grounds before the exam.

---

## Resources Used

### Primary Resources (Most Valuable)

#### 1. TryHackMe (Foundation Building)

**Learning paths completed:**
- Jr Penetration Tester (complete path)
- Web Application Pentesting (complete path)

**Why it was valuable:** THM provides structured learning with guided rooms that teach fundamentals without overwhelming you. Perfect for building a strong foundation when transitioning from defensive to offensive security.

#### 2. HackTheBox (Core Practice)

**Focus:** TJNull's OSCP preparation list

**Why it was valuable:** HTB machines on medium difficulty were often HARDER than the exam standalone machines. If you can consistently pwn HTB medium boxes, you're ready.

**Warning:** Don't get discouraged if HTB feels hard - it's supposed to be. The exam machines are more straightforward.

#### 3. Proving Grounds (Essential for Exam-Style Practice)

**Machines completed:** 10-15 boxes

**Why it was critical:** OffSec-style machines have a different "feel" than HTB. The enumeration patterns, exploitation chains, and privilege escalation paths follow OffSec's specific methodology. Doing PG machines directly contributed to my exam success.

**Recommendation:** Even 1 month of PG practice is enough if you focus on the right machines.

### Secondary Resources

#### 4. PortSwigger Web Security Academy

Used for deepening understanding of specific web vulnerabilities. The labs are excellent for SQL injection, authentication bypasses, and other web exploitation techniques.

#### 5. Bug Bounty Practice

Did some light bug bounty hunting on the side to experience what pentesting feels like against real targets (with permission, of course). This helped me understand the difference between lab environments and real-world scenarios.

#### 6. YouTube & Blog Posts

Used occasionally for specific topics, but not extensively. The three main platforms (THM, HTB, PG) were sufficient for passing.

### What I DIDN'T Use

- OffSec's official PWK course materials (PEN-200)
- OffSec's official lab environment
- Expensive OSCP prep courses
- Practice exams or "exam simulators"

**Bottom line:** THM, HTB, and PG alone are enough to pass OSCP+. Everything else is supplementary.

---

## Study Strategy

### The Learning Progression

**Phase 1 (Months 1-2): Foundation Building**
- Complete TryHackMe learning paths
- Focus on understanding fundamentals
- Build initial methodology

**Phase 2 (Months 3-5): Practical Application**
- Transition to HackTheBox machines (TJNull's list)
- Practice on Proving Grounds
- Bug bounty practice on the side
- PortSwigger labs for web vulnerabilities

**Phase 3 (Month 6): Exam Preparation**
- Increase study hours to 8-9/day
- Focus on OffSec-style boxes (PG)
- Review notes and refine methodology
- Practice documentation and report writing

### My Lab Methodology

For every machine, I followed this systematic approach:

#### Step 1: Solo Attempt (No Help)

Attempt to pwn the machine completely on my own. Struggling for a few hours is expected and valuable - this is where real learning happens.

#### Step 2: AI-Assisted Brainstorming (If Stuck)

When I hit a wall and had no new ideas, I'd use AI to:
- Generate new attack vectors to test
- Suggest enumeration techniques I might have missed
- Explain concepts I didn't fully understand

**Critical realization after 1 month:** Relying on AI during practice would cripple me during the exam. I stopped using AI entirely and forced myself to think independently.

#### Step 3: Walkthrough Reference (Last Resort)

If I was completely stuck after exhausting all my ideas, I'd consult the official walkthrough for ONLY the specific part where I was stuck, then continue on my own.

#### Step 4: Documentation (Most Important)

After every machine, I created a detailed Obsidian note documenting:

**If I pwned it:**
- Complete attack chain from initial foothold to root
- Commands that worked
- Why they worked
- What helped me deduce the correct path

**If I failed:**
- Where I got stuck
- What I missed
- What I should have done differently
- Lessons learned for future boxes

### Note-Taking System: Obsidian Knowledge Base

I built a comprehensive knowledge base in Obsidian containing:

✅ **Command cheatsheets** - every useful command I thought I'd need
✅ **Box writeups** - detailed notes on every machine I attempted
✅ **Lessons learned** - specific insights from failures and successes
✅ **Exploitation techniques** - organized by category (web, privesc, AD, etc.)
✅ **Enumeration methodologies** - systematic approaches for different scenarios

**Why this was crucial:** During the exam, I had instant access to my entire knowledge base. No googling, no guessing - just structured information I'd built through months of practice.

**Recommendation:** Having your own knowledge base available during the exam is invaluable, especially under time pressure. Start building it from day one.

### The AI Dependency Trap

**Important lesson:** At the beginning, I used AI (ChatGPT, Claude) to help me with boxes and commands. After a month, I realized this was a crutch that would hurt me during the exam.

**What I did:** Stopped using AI completely during lab practice. Forced myself to:
- Think through problems independently
- Develop my own enumeration methodology
- Build problem-solving skills without external assistance

**Result:** During the exam, I didn't feel lost without AI. My thought process was internalized, not dependent on external tools.

---

## Lab Experience

### Machine Difficulty Comparison

**HackTheBox (Medium difficulty) vs. OSCP+ Exam Standalone Machines:**

HTB medium boxes were generally HARDER than the exam's standalone machines. This is good news - if you can consistently pwn HTB medium boxes, you're over-prepared.

**Proving Grounds vs. OSCP+ Exam:**

PG machines felt much more similar to the exam in terms of:
- Enumeration patterns
- Exploitation methodology
- Privilege escalation techniques
- Overall "feel" and approach

**Recommendation:** Do both HTB (for difficulty) and PG (for exam-style methodology).

### Total Machines Completed

- **HackTheBox:** ~60-65 boxes (focused on TJNull's OSCP list)
- **Proving Grounds:** 10-15 boxes
- **TryHackMe:** Numerous guided rooms (not counted as "boxes")

**Was this overkill?** Probably. You could likely pass with 40-50 boxes if you practice deliberately and document well.

### Key Learning Moments

Some memorable "aha!" moments during lab practice:

1. **Understanding privilege escalation** isn't about memorizing exploits - it's about systematic enumeration
2. **Active Directory** is less scary than it seems when you break it down into logical steps
3. **Enumeration is 80% of the work** - if you enumerate properly, exploitation becomes obvious
4. **Documentation during practice saves time during exams** - the more you practice report writing, the faster you'll be

---

## The Exam

### First Attempt: Success

I passed OSCP+ on my **first attempt** in November 2025.

### Exam Structure

Without revealing specifics:
- 24-hour hands-on exam
- Multiple standalone machines
- Active Directory set (mandatory)
- Specific point requirements to pass
- Proctored via webcam throughout

### My Exam Timeline

**Hours 0-7: Complete Mental Block**

The first 6-7 hours were brutal. I was:
- Panicked
- Second-guessing every decision
- Unsure how long to spend on each machine
- Deeply disappointed in myself

I attempted all three standalone machines during this time. **Result: Zero machines pwned.**

This was terrifying. I considered giving up.

**Hours 7-9: The Turning Point (Active Directory)**

Instead of spiraling, I made a decision: pivot to the Active Directory set.

**Result:** I pwned the entire AD set in approximately 2 hours.

This success was a massive morale boost. Even though I didn't have much time left, I decided to keep pushing.

**Hours 9-22: The Comeback**

Re-approached one of the standalone machines I'd struggled with earlier. This time:
- Started completely fresh
- Re-did initial enumeration from scratch
- Stayed calm and methodical

**Discovery:** I had missed something obvious during my first attempt due to stress and panic.

Once I found the correct path, exploitation was straightforward.

**Hours 22-24: Final Checks & Sleep**

Around hour 22, I had enough points to pass. I:
- Verified I had all necessary screenshots
- Documented remaining commands and outputs
- Went to sleep (7 hours)

### Exam Difficulty

**Technical Difficulty:** 6/10

The machines themselves weren't exceptionally difficult compared to my lab practice. If I'd been calm and methodical from the start, I could have finished much faster.

**Mental Difficulty:** 9/10

The time pressure, proctoring, and stakes created immense psychological pressure. This was my first OffSec exam, and the mental challenge was far greater than the technical challenge.

### What I Would Do Differently

**Better Documentation During the Exam**

I should have documented my steps more thoroughly in real-time. When it came time to write the report, I realized I was missing:
- Some important screenshots
- Specific command outputs
- Exact exploitation steps

Thankfully, this didn't cost me the exam, but it caused unnecessary worry.

**Recommendation:** After EVERY successful step during the exam, immediately:
1. Take a screenshot
2. Copy the command into your notes
3. Save the terminal output
4. Document what you just did and why

Don't wait until the end to "remember" everything.

**Start with Active Directory First (Maybe)**

In hindsight, starting with AD might have been better for me. It was my strength, and getting an early win would have boosted my confidence immediately.

However, this is personal preference. Some people prefer saving AD for when they're tired.

**Trust My Preparation**

The biggest mistake was doubting myself during hours 0-7. I was well-prepared, but panic made me second-guess everything. If I'd trusted my methodology from the start, I would have had a much smoother exam.

---

## Report Writing

### Timeline

- **Exam ended:** Hour 24
- **Sleep:** 7 hours
- **Report writing:** 6-7 hours
- **Total time from exam start to report submission:** ~37 hours

### Template Used

I used the official template provided by Offensive Security in the exam guide. It includes all required sections and formatting.

### Technical Issues

The report took longer than it should have due to technical issues with my Microsoft Office installation. 

**Lesson learned:** Test your report template and Office installation BEFORE the exam. Don't discover compatibility issues when you're exhausted and on a deadline.

### Report Writing Tips

1. **Be thorough** - don't risk failing after successfully pwning enough machines just because of missing documentation
2. **Use the official template** - it has everything OffSec expects to see
3. **Include ALL required screenshots:**
   - Initial foothold proof
   - Privilege escalation proof
   - Flag contents (local.txt, proof.txt)
   - Key exploitation steps
4. **Explain your methodology** - show your thought process, not just commands
5. **Proofread before submitting** - typos won't fail you, but clarity matters

**Time recommendation:** Budget at least 6-8 hours for report writing. Don't rush this part.

---

## Skills Gained

### Technical Skills

The top technical skills I gained from OSCP+:

1. **Systematic Enumeration Methodology**
   - Not just running tools, but understanding what to look for
   - Knowing what information matters vs. noise
   - Building a repeatable process for any target

2. **Privilege Escalation Expertise**
   - Linux privilege escalation techniques
   - Windows privilege escalation techniques
   - Recognizing exploitation opportunities quickly

3. **Active Directory Attack Chains**
   - Kerberoasting, ASREPRoasting, credential dumping
   - Lateral movement techniques
   - Domain privilege escalation
   - Understanding AD trust relationships

4. **Web Application Exploitation**
   - SQL injection, LFI/RFI, authentication bypasses
   - Reverse shell generation and stabilization
   - Web enumeration beyond just running dirb/gobuster

5. **Post-Exploitation & Persistence**
   - Maintaining access after initial compromise
   - Pivoting through networks
   - Credential harvesting and reuse

### Soft Skills & Mindset Changes

The top non-technical lessons from OSCP+:

1. **Resilience Under Pressure**
   - The biggest lesson: never let despair and momentary frustration affect you
   - If I had given in to panic during hours 0-7, I would have failed
   - Keeping my cool and trying until the end was what made me succeed

2. **Methodical Problem-Solving**
   - Breaking complex problems into smaller steps
   - Not jumping to conclusions or "hail mary" attempts
   - Trusting the process even when stuck

3. **Attention to Detail**
   - Small details matter (missed config files, subtle misconfigurations)
   - Proper documentation prevents major headaches
   - Thoroughness beats speed in pentesting

**The Most Important Lesson:**

During the exam, when I was stuck for 7 hours with zero machines pwned, I had two choices:
- Give up and fail
- Keep trying despite the frustration

Choosing resilience over despair was the difference between passing and failing. This mindset applies beyond just OSCP - it's valuable for life.

---

## Career Impact

### Has OSCP+ Impacted My Career?

The honest answer depends on **where you live**.

**In Israel (where I'm based):**
- OSCP+ is **less valuable than practical experience** in the job market
- Many employers don't fully understand what the certification entails
- Having 1-2 years of pentesting experience often matters more than the cert
- However, it does help differentiate you from candidates with only theoretical knowledge

**Internationally (based on what I've seen):**
- OSCP+ carries significant weight, especially in the US, UK, and EU
- Often listed as "required" or "preferred" for junior/mid-level pentest roles
- Signals to employers that you can actually hack, not just theorize

### Was It Worth It?

**For me personally:** Yes.

Even if the certification itself doesn't open every door in Israel, the skills I gained are invaluable. I'm now confident in my ability to:
- Conduct real penetration tests
- Identify and exploit vulnerabilities
- Think like an attacker
- Document findings professionally

The certification validates what I can do, even if not every employer recognizes it.

---

## Evaluation & Recommendations

### Difficulty Rating

**Technical Difficulty:** 6/10

The machines themselves aren't exceptionally hard. They're realistic and require solid fundamentals, but they're not CTF-level difficulty.

**Overall Challenge (including time pressure + documentation):** 7/10

The combination of time pressure, proctoring, documentation requirements, and mental stress makes this challenging even if you're technically prepared.

### Value Rating

**Certification Value:** 7/10

Highly practical, internationally recognized, and proves hands-on skills. However, value varies significantly by geographic location and industry.

### Cost Analysis

| Item | Cost (USD) |
|------|------------|
| OSCP+ Exam Registration | $1,649 |
| HackTheBox Subscription (3 months) | ~$60 |
| Proving Grounds (1 month) | ~$20 |
| TryHackMe Subscription | ~$10/month |
| **Total** | **~$1,750** |

**Note:** I did NOT purchase OffSec's official PWK course ($1,649 includes one exam attempt + course materials, but I opted for self-study instead).

**Was it worth the investment?** 

Yes, but only because:
1. I passed on the first attempt (no retake fees)
2. I had the time to study full-time for 6 months
3. I genuinely enjoy offensive security

If any of those weren't true, the ROI would be questionable.

### Who Should Pursue OSCP+?

**I would recommend OSCP+ to:**

✅ Cybersecurity practitioners with **at least 1 year of experience** in a security role
✅ People who have completed 30-50 practice boxes and feel comfortable with the methodology
✅ Those willing to commit 4-6 months of serious study time
✅ Anyone passionate about offensive security (not just chasing a cert)

**I would NOT recommend OSCP+ to:**

❌ Complete beginners with zero cybersecurity experience
❌ People expecting a quick win (this takes months of dedication)
❌ Those looking for a "resume checkbox" without genuine interest
❌ Anyone unable to afford the cost + time investment

**Why not for beginners?**

OSCP+ is expensive (~$1,750+) and requires significant time investment. For someone with zero experience:
1. The learning curve is too steep
2. The failure rate is high (expensive retakes)
3. You'll spend months just building foundational skills

Better path for beginners:
1. Build fundamentals with free resources (TryHackMe, HackTheBox free tier)
2. Get 1-2 years of experience in a security role (SOC, security analyst, etc.)
3. Practice on 30-50 boxes before considering OSCP+
4. THEN pursue the certification when you're ready

---

## Tips for Future OSCP+ Candidates

### Before Starting

1. **Build a strong foundation first**
   - Complete TryHackMe learning paths (Jr Penetration Tester, Web App Pentesting)
   - Pwn at least 20-30 easy boxes before attempting medium difficulty
   - Understand basic Linux/Windows administration

2. **Set up your note-taking system early**
   - Choose a tool (Obsidian, CherryTree, Notion)
   - Start building your knowledge base from day one
   - Document everything, even small wins

3. **Budget realistically**
   - Exam fee + lab subscriptions + potential retakes
   - 4-6 months of focused study time
   - Consider opportunity cost if studying full-time

### During Preparation

1. **Follow TJNull's OSCP list religiously**
   - It's curated specifically for OSCP preparation
   - Covers all necessary attack vectors
   - Appropriate difficulty progression

2. **Practice on Proving Grounds before the exam**
   - Even 1 month is enough
   - Focus on 10-15 OffSec-style boxes
   - The methodology matters, not just the number of boxes

3. **Avoid AI dependency**
   - Use it early for learning, but wean yourself off
   - Force yourself to think independently
   - Build your own problem-solving process

4. **Document like you're writing a report**
   - Practice documentation during labs
   - Take screenshots of every successful step
   - Write up at least 5-10 complete "mock reports" during practice

5. **Master Active Directory**
   - AD is mandatory in OSCP+
   - It's often the "easiest" point gain if you're prepared
   - Practice AD chains until they're second nature

### Exam Day Tips

1. **Trust your preparation**
   - You've done the work, trust your methodology
   - Panic is your enemy, not the machines
   - If you're stuck, take a break and come back fresh

2. **Document in real-time**
   - Screenshot immediately after every successful step
   - Don't wait until the end to "remember" everything
   - Copy commands and outputs into your notes continuously

3. **Start with your strength**
   - If you're strong in AD, consider doing it first for an early win
   - If you prefer standalones, start there
   - An early success builds momentum and confidence

4. **Don't spend more than 2 hours stuck on one machine**
   - If you're making no progress after 2 hours, pivot
   - Come back later with fresh eyes
   - There's no prize for stubbornness

5. **Take breaks**
   - Schedule breaks every 3-4 hours
   - Step away from the computer, eat, hydrate
   - Your brain needs rest to function optimally

6. **Sleep before the report**
   - Don't try to power through 24+ hours without sleep
   - Your report will be better after rest
   - Budget time for both exam and report writing

---

## Common Pitfalls to Avoid

### 1. Over-Reliance on AI During Practice

Using ChatGPT/Claude to solve labs for you will cripple your exam performance. Use it to learn, then force yourself to work independently.

### 2. Skipping Proving Grounds

HTB alone isn't enough. OffSec-style machines have a different feel. Do at least 10-15 PG boxes before the exam.

### 3. Poor Documentation Habits

Practicing without proper documentation is wasted effort. If you don't document during practice, you won't do it well during the exam.

### 4. Rushing Into the Exam Too Early

If you're consistently struggling with medium boxes, you're not ready. Be honest with yourself about your skill level.

### 5. Neglecting Active Directory

AD is mandatory and often the "easiest" point gain. Not being prepared for AD basically guarantees failure.

### 6. Ignoring Mental Preparation

The exam is as much mental as technical. Practice staying calm under pressure during your lab work.

### 7. Not Testing Your Report Template

Don't discover technical issues with your Office installation during report writing. Test everything beforehand.

---

## What's Next?

### Future Certifications

My planned progression:

1. **OSWP (Offensive Security Wireless Professional)** - Next on my list
2. **OSWE (Offensive Security Web Expert)** - Following OSWP
3. Potentially OSEP or OSED after gaining more experience

### Long-Term Career Goals

OSCP+ is a stepping stone, not the destination. My goals:

- Transition fully from defensive (SOC) to offensive security roles
- Gain 2-3 years of practical pentesting experience
- Pursue advanced certifications (OSEP, OSWE, OSED)
- Contribute to the security community through writeups and tools

---

## Conclusion

OSCP+ was one of the most challenging and rewarding experiences of my career so far. The certification itself may not open every door (especially in Israel), but the skills I gained are invaluable.

**Key Takeaways:**

✅ **It's passable with self-study** - you don't need expensive boot camps
✅ **Mental resilience matters more than technical skill** - staying calm under pressure is crucial
✅ **Documentation is as important as exploitation** - practice it during labs
✅ **The journey teaches you more than the destination** - the process of preparing is where real learning happens

**Would I recommend it?**

Yes, but with caveats:
- Only if you have at least 1 year of cybersecurity experience
- Only if you're genuinely passionate about offensive security
- Only if you can commit 4-6 months to serious preparation
- Only if you understand the certification's value varies by location

If you meet those criteria, OSCP+ is absolutely worth pursuing.

---

**Final Rating:** 8/10

**Would I Do It Again?** Absolutely yes.

**Most Important Lesson:** During my worst moment in the exam - 7 hours in with zero machines pwned - I had a choice between giving up and persevering. Choosing resilience over despair made all the difference. This mindset applies far beyond OSCP.

Good luck to everyone on their OSCP+ journey. You've got this. Try Harder. 🚀

---

**Resources:**
- [Offensive Security OSCP+](https://www.offensive-security.com/pwk-oscp/)
- [TJNull's OSCP Preparation List](https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/)
- [TryHackMe](https://tryhackme.com/)
- [HackTheBox](https://www.hackthebox.com/)
- [Proving Grounds](https://www.offensive-security.com/labs/)
- [My Writeups & Notes](https://rootinator.com/)
