# 🧨 DNS Cache Poisoning – Exercise 2 (BIND9)

This repository contains the solution to **Exercise 2 – BIND9 DNS Cache Poisoning** from the Cybersecurity Lab (67607) at the Hebrew University.

---

## 📚 Overview

This exercise simulates a DNS cache poisoning attack on a vulnerable BIND9 resolver.  
The goal is to inject a **spoofed DNS record** into the resolver's cache by predicting TXID and source port, exploiting a **weak pseudo-random number generator**.

---

## 🛠️ Files Included

| File            | Description                                                   |
|-----------------|---------------------------------------------------------------|
| `ex2_client.c`  | Attacker's client: crafts and sends spoofed DNS responses     |
| `ex2_server.c`  | Attacker’s authoritative DNS server emulator                  |
| `explanation.txt` | Detailed description of attack phases and implementation   |
| `readme.txt`    | Contains submitter IDs in the format `id1,id2`                |
| `.gitignore`    | Ignores `.zip`, `.vmdk`, and irrelevant files                 |

---

## 📌 Attack Phases

### 🛰️ Attacker’s Server (`ex2_server.c`)
- Listens for DNS queries.
- For **odd TXIDs**: responds with a CNAME pointing to a random subdomain.
- For **even TXIDs**: responds with a CNAME to the target domain, sends TXID + port to attacker client.
- Stops once a valid TXID was sent.

### 🧠 Attacker’s Client (`ex2_client.c`)
- Sends initial query to trigger DNS request from the resolver.
- Receives TXID and source port from the server.
- Predicts next 10 TXIDs using an LFSR (Linear Feedback Shift Register).
- Sends spoofed DNS responses using raw sockets with forged source IP.
  - Includes A record (`www.example.cybercourse.com` → `6.6.6.6`)
  - Authority NS records for `cybercourse.com`
  - Additional record for NS server IP (`192.168.1.204`)

---

## 🚀 How to Run

1. **Compile** both server and client:
   ```bash
   gcc -o server ex2_server.c -lldns
   gcc -o client ex2_client.c -lldns
