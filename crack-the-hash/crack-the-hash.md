#

![Crack-the-hash_Header](./_attachment/thm-crack-the-hash-header.png)
Writeup by: Frederick Pellerin - `https://tryhackme.com/room/crackthehash`

---

## Overview

No surprises here,  this is a cracking hash focused room.  No theory.  Just a bunch of hash to crack on our own.

Here are different ways to crack hashes.  But first we need to know what type of hash file we got.

### Online tools

At first, let's use free online tools.

There a website, `hashes.com` that could be helpful with hash related stuff.

There another one named : `crackstation.net`.
This is an online hash cracking tool. Paste the hash in the box, click "I'm not a robot" and "Crack Hashes".  Voila! Quick and easy.

`https://hashcat.net/wiki/doku.php?id=example_hashes`
This web site has a vast collection of hashes.  They are classified by Hash-Mode and Hash-Name.  Handy when comes time to identify the hash mode to crack.

Online tools are useful to a certain point.  
Let's adjust our strategy.

---

### Hashcat

`hashcat` use GPU computing power and is crazy fast.

```shell
❯ hashcat -m 3200 "\$2y\$12\$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom" /usr/share/wordlists/rockyou.txt
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 3.1+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 15.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
==================================================================================================================================================
* Device #1: pthread-sandybridge-Intel(R) Core(TM) i7-6700K CPU @ 4.00GHz, 2912/5888 MB (1024 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 72

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt

Watchdog: Temperature abort trigger set to 90c

Host memory required for this attack: 0 MB

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 1 sec

Cracking performance lower than expected?                 

* Append -w 3 to the commandline.
  This can cause your screen to lag.

* Append -S to the commandline.
  This has a drastic speed impact but can be better for specific attacks.
  Typical scenarios are a small wordlist but a large ruleset.

* Update your backend API runtime / driver the right way:
  https://hashcat.net/faq/wrongdriver

* Create more work items to make use of your parallelization power:
  https://hashcat.net/faq/morework

[s]tatus [p]ause [b]ypass [c]heckpoint [f]inish [q]uit => s

Session..........: hashcat
Status...........: Running
Hash.Mode........: 3200 (bcrypt $2*$, Blowfish (Unix))
Hash.Target......: $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX...8wsRom
Time.Started.....: Fri Apr 14 12:25:15 2023 (1 min, 16 secs)
Time.Estimated...: Thu May  4 01:31:42 2023 (19 days, 13 hours)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:        8 H/s (6.37ms) @ Accel:2 Loops:64 Thr:1 Vec:1
Recovered........: 0/1 (0.00%) Digests (total), 0/1 (0.00%) Digests (new)
Progress.........: 636/14344385 (0.00%)
Rejected.........: 0/636 (0.00%)
Restore.Point....: 636/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:2112-2176
Candidate.Engine.: Device Generator
Candidates.#1....: fuckyou2 -> pebbles
Hardware.Mon.#1..: Util: 94%

[s]tatus [p]ause [b]ypass [c]heckpoint [f]inish [q]uit => 


```

We have a match!  `xXxXxXxX`.

---

`279412f945939ba78ce0758d3fd83daa`

`xXxXxxXxXxXXxXXxX`

Done!

## Task 2 - Level 2

> [!QUOTE]
> All of the answers will be in the classic rock you password list

---

Hash: F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85

`xXxXXxxXXXxxX`

---

Hash: 1DFECA0C002AE40B8619ECF94819CC1B

`xXxXXxxxxxx`

---

Hash: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.

Salt: aReallyHardSalt

`xXxxXxXXXXxxxX`

---

Hash: e5d8870e5bdd26602cab8dbe07a942c8669e56d6

Salt: tryhackme

```shell
❯ hashcat -m120 -a 0 hash.txt /usr/share/wordlists/rockyou.txt -o pass.txt -w 4
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 3.1+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 15.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
==================================================================================================================================================
* Device #1: pthread-sandybridge-Intel(R) Core(TM) i7-6700K CPU @ 4.00GHz, 2912/5888 MB (1024 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimim salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

INFO: All hashes found as potfile and/or empty entries! Use --show to display them.

Started: Fri Apr 14 14:39:11 2023
Stopped: Fri Apr 14 14:39:11 2023
❯ hashcat -m120 -a 0 hash.txt /usr/share/wordlists/rockyou.txt -o pass.txt -w 4 --show
❯ hashcat --show -m 160 hash.txt
e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme:XxXxXxXxXXxXxXxX
```

That's it!

## ROOM COMPLETED
