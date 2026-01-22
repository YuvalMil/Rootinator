---
layout: post
title: "My First Bug Bounty: Finding an IDOR Vulnerability"
date: 2026-01-15 09:00:00 +0200
categories: [bug-bounty]
tags: [idor, web-security, vulnerability-research]
featured_image: "https://images.unsplash.com/photo-1555949963-ff9fe0c870eb?w=800&h=400&fit=crop"
excerpt: "The story of how I found and reported my first IDOR vulnerability, earning my first bug bounty payment."
---

Everyone remembers their first bug bounty. This is the story of how I found an Insecure Direct Object Reference (IDOR) vulnerability that earned me my first payout and taught me valuable lessons about security research.

## The Discovery

I was testing a popular e-commerce platform's API when I noticed something interesting in the purchase history endpoint:

```
GET /api/v1/orders/12345
Authorization: Bearer <token>
```

The response included order details for order #12345. What if I changed the order ID?

## The Vulnerability

### Initial Test

I modified the request:

```
GET /api/v1/orders/12346
Authorization: Bearer <token>
```

To my surprise, I received complete order details for someone else's purchase, including:
- Full name and address
- Email and phone number
- Order contents
- Payment method (last 4 digits)

### Impact Assessment

**Severity**: High

**Why?**
- Exposed PII (Personally Identifiable Information)
- No rate limiting
- Sequential order IDs (easy enumeration)
- Affected all users

## The Reporting Process

### Step 1: Document Everything

I created a detailed report including:

```markdown
# IDOR Vulnerability in Order API

## Summary
Insecure Direct Object Reference allowing unauthorized access to user orders

## Steps to Reproduce
1. Authenticate as normal user
2. Access own order: GET /api/v1/orders/12345
3. Modify order ID: GET /api/v1/orders/12346
4. Observe unauthorized access to other user's data

## Proof of Concept
[Screenshots attached]

## Impact
- Exposure of PII for all users
- Privacy violation
- Potential GDPR violation

## Recommended Fix
- Implement proper authorization checks
- Use UUIDs instead of sequential IDs
- Add rate limiting
```

### Step 2: Submit Through Proper Channels

I submitted through HackerOne:
- Used the company's security program
- Followed responsible disclosure timeline
- Provided clear reproduction steps

### Step 3: Wait and Communicate

Timeline:
- **Day 1**: Submitted report
- **Day 3**: Triaged and validated
- **Day 7**: Fix deployed
- **Day 14**: Bounty awarded ($500)
- **Day 30**: Public disclosure

## Lessons Learned

### What Worked Well

1. **Clear Documentation**: Screenshots and step-by-step reproduction
2. **Professional Tone**: Respectful and helpful communication
3. **Impact Explanation**: Clearly stated the severity
4. **Patience**: Gave team time to fix properly

## Key Takeaways

1. **Start Simple**: IDORs are common and impactful
2. **Be Thorough**: Test all endpoints systematically
3. **Stay Ethical**: Never access more data than needed for PoC
4. **Communicate Clearly**: Good reports get faster responses
5. **Be Patient**: Quality over quantity

## Conclusion

Finding your first bug bounty is an incredible feeling. It validates your skills and opens doors to a rewarding career in security research. My advice? Start testing, stay curious, and always act ethically.

The next bounty could be yours! 🎯

---

*Disclosure: All technical details have been anonymized and published with permission after the vulnerability was fixed.*