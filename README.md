# Network-Traffic-Analysis-with-Wireshark-Detecting-a-SYN-Flood-Attack

This project demonstrates how to analyze network traffic using Wireshark to detect malicious activity.
The provided **TCP/HTTP** log captures both normal employee web traffic and a **malicious SYN flood attack** against a company’s web server.

Tools Used: Wireshark

Focus: TCP three-way handshake, HTTP requests, SYN flood attack

Skills Demonstrated: Packet analysis, identifying DoS activity, documenting findings, proposing mitigations

# Normal Traffic (Log Entries 47–51)
- Log starts at **entry #47**, ~3.144 seconds after capture began.  
- **192.0.2.1** is the company’s **web server**.  
- **198.51.100.0/24** are employee machines.  
- Example of a normal TCP 3-way handshake:  
  1. `[SYN]` – Client requests connection  
  2. `[SYN, ACK]` – Server responds  
  3. `[ACK]` – Client acknowledges
 
<img width="658" height="256" alt="Screenshot 2025-10-01 at 4 26 19 PM" src="https://github.com/user-attachments/assets/f6788f40-4699-48fd-b16f-41ae70c17503" />
 
- After handshake:  
  - Employee requests `sales.html` via HTTP  
  - Server responds with `200 OK`  

This shows **normal browsing behavior**.

## Attack Traffic (Log Entries 52+)
- **203.0.113.0** begins sending repeated `[SYN]` packets to port 443(encrypted web traffic port).  
- These **never complete the handshake**, which is suspicious.  
- This behavior is consistent with a **SYN Flood DoS attack**.

<img width="655" height="479" alt="Screenshot 2025-10-01 at 4 28 09 PM" src="https://github.com/user-attachments/assets/66dcdb43-e443-4847-a754-a1901cfa1866" />


### Key Evidence:
- At first, server responds normally (`SYN, ACK`).  
- Soon, server struggles to keep up:  
  - Visitors see `504 Gateway Timeout` errors.  
  - Server sends `[RST, ACK]` packets to drop requests.  
- After **log entry #125**, only attacker traffic remains.

<img width="637" height="685" alt="Screenshot 2025-10-01 at 4 29 10 PM" src="https://github.com/user-attachments/assets/a9aef186-ba8a-44df-bbb7-72132fda5a43" />

- Legitimate users can no longer connect — the DoS attack succeeded.

---

## Summary Table of Log Behavior

| Log Range | Behavior | Notes |
|-----------|----------|-------|
| 47–51     | Normal handshake + HTTP GET/200 OK | Legit traffic |
| 52–54     | Attacker SYN requests start | Server still OK |
| 55–62     | Employee connects successfully | Server responds |
| 63–125    | SYN requests increase | Timeout & reset errors |
| 125+      | Only attack traffic remains | DoS successful |

---

## Mitigation Strategies
To defend against SYN Flood attacks, organizations can:  
- Configure **firewalls / IDS** (e.g., Snort, Suricata) to detect and block floods.  
- Enable **SYN cookies** to protect against resource exhaustion.  
- Apply **rate limiting** or **load balancing** to manage traffic.  
- Use **cloud-based DDoS protection services**.  

---

## Files in This Repo
- `Wireshark_TCP_HTTP_log.pdf` → Original network traffic log.  
- `README.md` → This analysis report.  
