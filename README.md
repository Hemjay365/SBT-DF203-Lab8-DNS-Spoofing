[README.md](https://github.com/user-attachments/files/32629391/README.md)
# SBT-DF203 — Lab 8: DNS Spoofing Forensics

Student: Mary-Joy Adewole
Registration Number: 2025/FWSD/11468
Institution: International Cybersecurity and Digital Forensics Academy (ICDFA)
Course: SBT-DF203 — Basic Networking Skills for Digital Forensics
Instructor: Aminu Idris
Delivery Block: 3/3 | 19–25 September 2026

## Overview

This repository documents a controlled, host-only ARP poisoning and DNS spoofing simulation against a
dedicated victim VM, using a reserved, non-existent training domain (`portal.icdfa.test`) and a harmless
warning page. A legitimate DNS/ARP/HTTP baseline was captured first, followed by a three-terminal
controlled run (packet capture, ARP poisoning, DNS interception) while the victim resolved the training
domain and loaded the page.

Key finding: ARP-layer evidence (`reports/arp_claims_during_spoof.tsv`) confirms two-way poisoning — the
analyst's MAC (`00:0c:29:a8:ed:ac`) falsely claimed as both the gateway (`192.168.74.1`) and the victim
(`192.168.74.129`) — and the victim's subsequent HTTP connection (`reports/post_dns_connections.tsv`)
reached the analyst server directly as a result.

Also documented: the instructor-supplied `dns_spoof.py` script was found, on pre-execution review, to
target real third-party domains (`google.com`, `facebook.com`, `ubalt.com`, `ubalt.edu`, `boogle.com`)
by default. This was identified and corrected to target only the reserved training domain before any
script was run — full diff in `scripts/` and Section 4.2 of the report.

## Repository Structure

```
report/
├── SBT-DF203-Lab8_2025-FWSD-11468_Mary-Joy-Adewole.docx
├── SBT-DF203-Lab8_2025-FWSD-11468_Mary-Joy-Adewole.pdf
│   Full lab report — network diagram, baseline evidence, script review/correction,
│   controlled spoofing timeline, ARP correlation, victim connection correlation,
│   alternative explanations, cleanup, and defensive recommendations.

evidence/
├── dns_baseline.pcapng        Legitimate baseline capture (pre-spoofing)
└── dns_spoof_controlled.pcapng   Controlled ARP poisoning + DNS spoof capture

scripts/
├── arp.py                          Instructor-supplied ARP spoofing utility (reviewed, unmodified)
├── dns_spoof.py                    Instructor-supplied DNS interception script (corrected — see below)
└── dns_spoof_original_backup.py    Unmodified original, kept for comparison

reports/
├── dns_baseline_sha256.txt / dns_spoof_capture_sha256.txt   Evidence hashes
├── ip_forward_before.txt / iptables_before.rules            Pre-flight state
├── direct_page_test.html                                    Pre-flight reachability test
├── arp_before.txt / arp_after_cleanup.txt                    ARP table, before and after
├── dns_script_review.txt / dns_script_review_corrected.txt / dns_script_diff.txt
│     Script review and the correction applied to dns_spoof.py's hostDict
├── arp_claims_during_spoof.tsv       ARP opcode-2 replies during the event
├── post_dns_connections.tsv          Victim's TCP/HTTP connection after DNS resolution
├── dns_all_fields.tsv / dns_portal_only.tsv   Full and filtered DNS field extraction
└── iptables_after_cleanup.txt / process_cleanup_check.txt   Post-run cleanup verification

screenshots/
20 terminal/browser screenshots referenced as Figures 1–20 in the report, covering setup,
baseline capture, script review/correction, the controlled spoofing run, packet-level
evidence, and cleanup verification.
```

## Evidence Integrity

| File | SHA-256 |
|---|---|
| `evidence/dns_baseline.pcapng` | `da1214d76e1fd25e41ee3df759fd9017b281a3f5311c751012bad70c8d45f21f` |
| `evidence/dns_spoof_controlled.pcapng` | see `reports/dns_spoof_capture_sha256.txt` |

## Environment Note

The instructor-supplied `dns_spoof.py` script's default `hostDict` targeted five real, third-party
domains rather than the reserved training domain. This was caught on pre-execution review, backed up
as `dns_spoof_original_backup.py`, and corrected to target only `portal.icdfa.test` before the script
was ever run — see `reports/dns_script_diff.txt` and Section 4.2 of the report. Separately, the analyst
VM's network adapter required a second (NAT) interface added mid-lab to restore internet access for
package installation, alongside the host-only interface used for the attack path; this is documented
in the report rather than omitted.

## Tools Used

- `arp.py` / `dns_spoof.py` (scapy, NetfilterQueue) — controlled ARP poisoning and DNS interception
- `dnsmasq` — training-domain resolver, bound to the host-only interface only
- Wireshark / `tshark` — packet capture and field-level DNS/ARP/HTTP analysis
- `sha256sum` — evidence integrity verification

## Academic Integrity Statement

I confirm that I completed this lab in an authorized, host-only environment, preserved the supplied
evidence, did not target any third-party system, and accurately documented my own commands,
observations, and conclusions.
