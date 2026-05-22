# Net_Practice — Complete Tutorial (Mandatory + Bonus-Oriented Defense Prep)

> **42 Barcelona** | Networking fundamentals project

This guide is written for someone starting from zero and aims to get you to the end of the project confidently.

---

## Table of Contents

1. [What this project is](#1-what-this-project-is)
2. [What you must submit (mandatory)](#2-what-you-must-submit-mandatory)
3. [About the “bonus” in NetPractice](#3-about-the-bonus-in-netpractice)
4. [How to open and use the interface](#4-how-to-open-and-use-the-interface)
5. [Networking fundamentals you need](#5-networking-fundamentals-you-need)
6. [Subnetting quick method (must memorize)](#6-subnetting-quick-method-must-memorize)
7. [Router and route logic (must memorize)](#7-router-and-route-logic-must-memorize)
8. [Step-by-step method to solve any level](#8-step-by-step-method-to-solve-any-level)
9. [Common mistakes and how to debug fast](#9-common-mistakes-and-how-to-debug-fast)
10. [Training plan to finish all 10 levels](#10-training-plan-to-finish-all-10-levels)
11. [Defense preparation (random levels)](#11-defense-preparation-random-levels)
12. [Submission checklist](#12-submission-checklist)
13. [Useful resources](#13-useful-resources)

---

## 1. What this project is

NetPractice is a practical networking project where you fix broken network diagrams in a browser interface.

From the subject PDF:
- You solve **10 levels**.
- You work in a browser interface (`index.html`) from downloaded project files.
- You edit only unshaded fields.
- You use **Check again** to validate.
- You export each solved level with **Get my config**.

---

## 2. What you must submit (mandatory)

You must:
1. Enter your **42 login** in the interface.
2. Solve all **10 mandatory levels**.
3. Export **one config file per level** with **Get my config**.
4. Put the 10 files at the **repository root**.

Important (subject requirement): during defense, only what is in your Git repo is evaluated.

---

## 3. About the “bonus” in NetPractice

The subject PDF does not define a separate “Bonus part” chapter like some other 42 projects.

In practice, the “extra” objective is:
- being able to solve **random levels during defense**,
- solving quickly without external tools,
- understanding networking deeply instead of memorizing one path.

So this tutorial includes a strong defense-focused training section as your bonus-oriented preparation.

---

## 4. How to open and use the interface

1. Download the attached files from the project page.
2. Extract them anywhere.
3. Open `index.html` in your browser.
4. Enter your login to practice your own submission flow.
5. For each level:
   - read the goal at top,
   - fix fields,
   - click **Check again**,
   - once passed, click **Get my config** and save the file.

Do **not** move to next level before exporting the current config.

---

## 5. Networking fundamentals you need

### 5.1 IPv4 and mask

An IPv4 address has 32 bits (e.g., `192.168.10.42`).
A subnet mask (or CIDR prefix) splits it into:
- **network bits**
- **host bits**

Example:
- `192.168.10.42/24` => mask `255.255.255.0`
- network = `192.168.10.0`
- broadcast = `192.168.10.255`
- usable hosts = `.1` to `.254`

### 5.2 CIDR essentials

- `/8` = `255.0.0.0`
- `/16` = `255.255.0.0`
- `/24` = `255.255.255.0`
- `/25` = `255.255.255.128`
- `/26` = `255.255.255.192`
- `/27` = `255.255.255.224`
- `/28` = `255.255.255.240`
- `/29` = `255.255.255.248`
- `/30` = `255.255.255.252`

### 5.3 Network / broadcast / host range

For a subnet:
- first address = **network address** (cannot assign to host)
- last address = **broadcast address** (cannot assign to host)
- middle addresses = usable hosts

Formula:
- hosts per subnet = `2^(host_bits) - 2`

### 5.4 Gateway and routing

- If destination is in same subnet: host sends directly.
- If destination is outside subnet: host sends to **default gateway**.
- Routers forward packets using routing table:
  1. directly connected network
  2. static route
  3. default route (if defined)

### 5.5 Switch vs Router

- **Switch**: forwards frames in same LAN.
- **Router**: connects different networks/subnets.

NetPractice errors usually come from wrong subnet logic or wrong route/gateway.

---

## 6. Subnetting quick method (must memorize)

Given `IP/prefix`, do this in order:

1. Find block size in the interesting octet:
   - block size = `256 - mask_octet`
2. Find the subnet boundary where IP belongs.
3. Compute:
   - network = boundary start
   - broadcast = next boundary - 1
   - host range = network+1 to broadcast-1

Example: `192.168.1.77/26`
- `/26` => mask `255.255.255.192` => block size `64`
- boundaries in last octet: `0, 64, 128, 192`
- 77 is in `64-127`
- network: `192.168.1.64`
- broadcast: `192.168.1.127`
- usable hosts: `192.168.1.65 - 192.168.1.126`

---

## 7. Router and route logic (must memorize)

When configuring a route, always think:

- **Destination network**: where I want to go.
- **Mask/prefix**: how big that destination network is.
- **Next hop**: immediate neighbor router IP that moves me closer.

Checklist for every route:
1. Is next hop in a directly connected subnet of this router?
2. Is route destination written as network address (not host address)?
3. Is mask correct?
4. Did I accidentally point to self/wrong interface?

For hosts:
- default gateway must be in host’s local subnet.

---

## 8. Step-by-step method to solve any level

Use this exact order every time:

1. **Read objective** at top.
2. **Map devices**: identify hosts, routers, interfaces, existing values.
3. **Identify each subnet** link by link.
4. **Validate host IP/mask pairs**:
   - same subnet where needed,
   - no network/broadcast addresses used as host.
5. **Set default gateways** on hosts.
6. **Set router interfaces** correctly.
7. **Add/fix routes** for non-direct networks.
8. **Check for overlaps/conflicts**.
9. Click **Check again**.
10. If green: **Get my config** immediately.

If red, use logs bottom-up and verify one hop at a time.

---

## 9. Common mistakes and how to debug fast

### Mistake A: IP looks valid but wrong subnet
Symptom: same cable, no connectivity.
Fix: recompute network address for both sides; they must match subnet.

### Mistake B: Using network or broadcast as host
Symptom: impossible communication even with “correct-looking” values.
Fix: compute network/broadcast and avoid assigning them.

### Mistake C: Wrong gateway
Symptom: local works, remote subnet fails.
Fix: gateway must be a router IP in same local subnet as host.

### Mistake D: Wrong route next hop
Symptom: route exists but traffic still dies.
Fix: next hop must be reachable directly from current router.

### Mistake E: Typo in mask/prefix
Symptom: random partial failures.
Fix: verify prefix and dotted mask consistency.

---

## 10. Training plan to finish all 10 levels

### Phase 1 — Fundamentals (Day 1)
- Master `/24`, `/25`, `/26`, `/27`, `/28`, `/30`.
- Do manual calculations on paper.

### Phase 2 — First pass all levels (Day 1-2)
- Solve all 10 levels once.
- Save every config.
- Note errors that repeat.

### Phase 3 — Second pass with timer (Day 2-3)
- Solve again from scratch.
- Target: reduce solving time each round.

### Phase 4 — Defense simulation (Day 3+)
- Randomize levels.
- Limit tool usage to what defense allows (mental math + simple calculator only if needed).
- Explain each decision out loud.

---

## 11. Defense preparation (random levels)

The subject states you must solve random levels during defense in limited time.

To prepare:

1. Practice without looking at old configs.
2. Use one fixed solving routine (Section 8).
3. Drill subnet calculations until automatic.
4. Be able to explain:
   - why two IPs are/aren’t in same subnet,
   - why a gateway is valid,
   - why a route next hop is correct.
5. Keep calm and debug by path hop-by-hop.

A strong defense answer is clear reasoning, not trial-and-error clicks.

---

## 12. Submission checklist

- [ ] I entered my 42 login in NetPractice.
- [ ] I solved all 10 mandatory levels.
- [ ] I exported one config file per solved level.
- [ ] I placed all 10 files at repository root.
- [ ] File names are exactly as exported or as required by evaluator.
- [ ] I can solve at least 3 random levels without external help.

---

## 13. Useful resources

Use these to reinforce fundamentals:

- 42 NetPractice subject (your `NetPractice.pdf` in this repo)
- Cloudflare Learning Center — *What is a subnet mask?*  
  https://www.cloudflare.com/learning/network-layer/what-is-a-subnet-mask/
- Cisco — *IP Addressing and Subnetting for New Users*  
  https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html
- Red Hat — *What is a default gateway?*  
  https://www.redhat.com/en/topics/linux/what-is-a-default-gateway
- IBM — *What is CIDR?*  
  https://www.ibm.com/topics/cidr
- Wikipedia (quick reference) — *Classless Inter-Domain Routing*  
  https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing

> During defense, external tools are not allowed (except simple calculator tolerance mentioned by subject). Use resources during training only.

---

If you want, I can next add a second file with **20 solved subnet drills + route drills** so you can train exactly for defense speed.
