# Testing Windows Code Integrity from Software

MSc Cyber Security dissertation by Muhammed Tariq Ali Razvi, University of Birmingham, September 2026. Supervised by Dr Tom Chothia.

- Final report: [report/final-report.pdf](report/final-report.pdf)
- Slides: [slides/final-slides.pdf](slides/final-slides.pdf)

## The question

Windows tells its users that kernel code is safe because Microsoft has checked it, and a driver is allowed to load because it carries a Microsoft signature. This project asks what that signature is really worth when the attacker is an administrator on an ordinary machine, using nothing but signed software, no exploit and no unsigned code.

## What I found

Reading is not protected. The signed forensic driver WinPmem handed over the whole physical memory of the machine, all 10,742 MB of it, and it also handed over the contents of processes that had already closed, so the signature was reassurance and not security.

Writing was different, but not because of permission. The driver gave a write handle without complaint, yet every write attempt brought the machine down before the target value had changed. Three of the four crash dumps that survived were Windows halting itself on purpose, because a check built into the kernel noticed its own structures no longer made sense. The thing that stopped the attack was not the signing check Windows advertises, and not a permission check, but a safety net that was never meant to be the defence.

A routine game update also broke the working offsets, which slowed the work until new ones were found, and that delay is not a security control either.

## How it was tested

The work is built as five stages, each adding one capability to the one before, so that any failure can be traced to the step that caused it. Stage 1 uses a purpose built dummy target as the control. Stage 2 uses a live game, reading its memory through the normal virtual memory route. Stage 3 searches a saved copy of the machine. Stage 4 reads physical memory through the signed driver, which is where the signing question is answered. Stage 5 asks whether the same route can also change memory.

Everything ran on one Windows 10 virtual machine under VirtualBox. The newer defences, VBS and HVCI, were not active and were not tested, so these results say nothing about them. The report states this limit openly and the findings should be read with it in mind.

## Why there is no code

The report releases no attack code, as its ethics chapter explains. Two short listings of ordinary file calls are shown in the report to explain the write test, and nothing else is published.

## Related work

This project builds on the question raised by the following paper, which reached physical memory on Windows by inducing memory aliasing and read it with WinPmem. My work goes back to the same driver and asks what it gives an administrator when no aliasing is induced at all. The paper is linked and not copied here.

- Sam Collins, Tom Chothia, William Burgess, Marius Muench and David Oswald. "Download More RAM: Dismantling Windows Operating System Defences with Mischievous Memory". USENIX Security Symposium, 2026.
  - Artifact: https://doi.org/10.5281/zenodo.20331553
  - Code: https://github.com/SamCollins1327/Download-More-RAM

## Credits

WinPmem is by Mike Cohen and Velocidex. All results, measurements and conclusions in the report are my own, with guidance from my supervisor.

All rights reserved. The report and slides are here to read and not to reuse.
