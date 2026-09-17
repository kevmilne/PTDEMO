# PTDEMO
This is the mainframe Pen Test demo from Vanguard.  HTML - start it in a browser.# Vanguard ONE-BOX

## AI-Guided Mainframe Penetration Testing with Live Threat Attribution

> **A demonstration of a real AI making penetration-testing decisions while AttribTNG independently makes attribution decisions from the activity it observes.**

This project was built as a conference demonstration around a deliberately vulnerable banking environment called **SighberBank**, backed by the **GIBSON** mainframe training platform.

This is not a scripted “AI hacker” animation.

The experiment demonstrates two independent decision processes operating at the same time:

1. a **real AI-led penetration test**, where the AI receives evidence, selects its next action, chooses from tools made available inside a controlled harness, observes the resulting output and decides what to do next; and
2. **AttribTNG**, which is not given the attack plan and instead builds an attribution hypothesis from the techniques and behaviour it observes.

A continuous background-noise generator adds normal activity, scanners, commodity-malware behaviour and unrelated threat-actor-like traffic so that attribution does not take place against an artificially clean attack stream.

The result is an **offensive-to-attribution experiment**: attacker decisions on one side and defender observations on the other.

---

## What Makes This Different?

A lot of “AI cyber” demonstrations reduce the AI to a chat interface wrapped around a predefined command sequence.

This experiment asks a different question:

> **What happens when an AI is allowed to make the next penetration-testing decision from the evidence it has actually received, while a separate defensive system attempts to work out what it is observing?**

The AI was placed inside a controlled penetration-testing harness. It was given a defined lab environment, an authorised collection of security tools and decision points at which it had to assess the evidence before progressing.

The important loop is:

```text
OBSERVE
   ↓
ASSESS
   ↓
DECIDE
   ↓
SELECT TOOL
   ↓
EXECUTE
   ↓
READ OUTPUT
   ↓
GATE / REASSESS
   ↓
NEXT DECISION
```

At the same time:

```text
NETWORK / HOST ACTIVITY
          ↓
        Zniff
          ↓
 observed behaviour / TTPs
          ↓
       AttribTNG
          ↓
 actor correlation + confidence
          ↓
 attribution hypothesis
```

---

## Important: Real Campaign vs Presentation Layer

This distinction matters.

The **underlying campaign was run inside a controlled penetration-testing harness**.

The attack inputs, security-tool activity, decision points, evidence gates and outputs represented by this demonstration come from that campaign.

The HTML in this repository is primarily the **conference presentation and replay layer**.

It does not contain a fake JavaScript LLM pretending to make decisions.

Instead, it presents the campaign evidence in a deterministic form that can be stepped through reliably during a live conference presentation.

The campaign data is derived from components including:

```text
campaign.py
live_tools.py
metasploit_foothold.py
decision_pane.py
attribution.py
threat-actors.json
navigator-matrix.json
```

In short:

> **The inputs, attacks, decision points, gates and outputs shown are based on the real controlled campaign. The HTML is the visualisation of that run, not the mechanism pretending to have performed it.**

---

## The Safe Harness

The AI was not simply given an unrestricted machine and told to “hack the bank.”

A controlled harness was constructed and a defined set of security tools was made available.

These include:

| Tool                      | Role                                                      |
| ------------------------- | --------------------------------------------------------- |
| **EZRecon**               | OSINT, DNS, subdomains, harvesting and document discovery |
| **GIBSON**                | Deliberately vulnerable z/OS/mainframe training target    |
| **ENUM**                  | Mainframe enumeration and APF/security discovery          |
| **ELV.***                 | Mainframe privilege-escalation research tooling           |
| **Nmap**                  | Network and service discovery                             |
| **Hydra**                 | Controlled credential spraying                            |
| **TShOcker**              | JES/FTP-assisted mainframe command execution              |
| **racf2john**             | RACF password-data conversion                             |
| **John the Ripper Jumbo** | Offline RACF password analysis                            |
| **curl / ffuf**           | API assessment                                            |
| **Metasploit**            | Controlled endpoint foothold and pivot                    |
| **Standard Kali tools**   | Supporting security utilities                             |

The AI's job is not merely to execute these tools.

Its job is to decide:

> **Which tool or technique is justified by the evidence I have right now?**

---

# Evidence-Gated Penetration Testing

The campaign separates:

```text
[dec]  assessment / decision
[atk]  offensive action
[vic]  tool or target output
[def]  defensive observation

GATE PASS
STOP
```

The campaign does not progress simply because the next command exists.

It progresses because the preceding action produced sufficient evidence.

Examples include:

* choosing a target from reconnaissance rather than being handed one;
* switching reconnaissance techniques when a source becomes rate-limited;
* verifying an SMTP path before relying on it;
* confirming a foothold before pivoting;
* discovering both Active Directory and the mainframe, then deciding that AD is not the objective;
* verifying credentials before mainframe access;
* enumerating APF libraries before choosing an escalation technique;
* independently checking privilege escalation instead of trusting the exploit's own success message;
* establishing an API baseline before testing for SQL injection.

This evidence-driven decision process is one of the central ideas behind the project.

---

# The Campaign

The demonstration contains eight major phases.

### 1. Reconnaissance → Target Selection

The AI begins with the organisation rather than a predefined victim.

It investigates DNS, subdomains, public documents, personnel information, email addresses, exposed credentials and mainframe-related information.

A target is then selected from that evidence.

### 2. Lure Construction

Reconnaissance determines whether an email route is viable and what information is available for constructing the scenario.

The mail path is verified before the campaign progresses.

### 3. Endpoint Foothold

The controlled victim endpoint provides an initial foothold.

The AI verifies the session, examines the network and discovers both the domain infrastructure and the mainframe.

It explicitly decides that **the mainframe is the engagement objective and Active Directory is not**.

### 4. Mainframe Access

Credentials gathered during reconnaissance are validated.

The campaign then demonstrates controlled FTP/JES-assisted mainframe execution.

### 5. Enumeration and Privilege Escalation

The AI first establishes:

```text
What privileges do I have?
Which APF libraries exist?
Which can I modify?
Which escalation path is actually justified?
```

Only then is the relevant escalation technique selected.

The result is independently verified.

### 6. RACF Collection and Analysis

The lab demonstrates RACF-related collection, controlled exfiltration and offline password analysis using tools including `racf2john` and John the Ripper.

### 7. Independent API Actor

A second scenario actor approaches the environment externally without using the first campaign's foothold.

This produces a different behavioural sequence for AttribTNG to observe.

### 8. API Assessment

The AI examines the CBSA API, establishes a response baseline, detects SQL-injection behaviour and follows the evidence into the DB2-backed application environment.

---

# AttribTNG

AttribTNG operates independently from the penetration-testing decision process.

It consumes observed techniques and compares them with actor behaviour profiles.

Conceptually:

```text
Observed activity
       ↓
Technique identification
       ↓
TTP correlation
       ↓
Actor profiles
       ↓
Confidence changes
       ↓
Lead attribution hypothesis
```

The system uses distinct observed techniques rather than simply counting repeated copies of the same event.

An actor must cross a correlation threshold before being presented as the lead candidate.

Importantly, attribution is deliberately shown as:

```text
probabilistic, not conclusive
```

The demonstration does not claim that matching ATT&CK techniques proves the identity of a real-world actor.

---

# Mainframe Behaviour Mapping

The navigator also contains mainframe-specific behaviours, including:

```text
MF-IA-VALID   Valid TSO/FTP credential
MF-EX-JES     JES job submission / REXX
MF-DISC-ENUM  Privilege and APF enumeration
MF-PE-APF     APF library abuse
MF-CA-RACFDS  RACF credential-store access
MF-COLL-STAGE Credential-store staging
MF-EXF-FTP    Mainframe-native FTP exfiltration
MF-API-SAF    z/OS Connect / SAF boundary activity
MF-API-SQLI   CBSA SQL injection
```

These cells illuminate as corresponding behaviour is observed.

---

# Background Noise

Real networks are not clean.

AttribTNG should not succeed simply because it is fed an attack stream containing nothing except the desired actor.

The demonstration therefore runs a continuous **background traffic generator**.

It includes normal activity such as:

* TSO sessions
* DB2 activity
* authorised FTP
* CICS transactions
* JES jobs
* RACF auditing
* TN3270 traffic
* z/OS Connect monitoring
* USS activity
* ICSF activity

It also introduces suspicious and malicious-looking traffic such as:

* Internet scanning
* TN3270 probes
* FTP spraying
* commodity malware patterns
* unrelated C2 behaviour
* activity resembling multiple different threat-actor profiles

Some of those background events contribute competing TTP evidence to the attribution engine.

This means the problem is not:

> “Can AttribTNG recognise the only attack occurring?”

It is:

> **“Can useful attribution evidence emerge while unrelated benign and malicious-looking activity is occurring at the same time?”**

---

# Zniff

Zniff provides the sensor view used during the demonstration.

Activity is broadly classified as:

```text
GREEN  normal / expected
AMBER  suspicious / interesting
RED    high-interest / hostile
```

Zniff observes.

AttribTNG correlates.

They are deliberately separate functions.

---

# Two Independent Questions

The entire demonstration can be reduced to two simultaneous questions.

### Offensive

```text
Given what I have just learned,
what should I test next?
```

### Defensive

```text
Given what I am seeing,
what does this behaviour resemble?
```

That is the core experiment.

---

# Presentation Interface

The conference interface provides:

### Decision Engine

* reasoning;
* tool selection;
* actions;
* outputs;
* decisions;
* evidence gates.

### Attribution View

* actor candidates;
* matched TTPs;
* confidence;
* lead-attribution hypothesis;
* mainframe behaviour matrix.

### Supporting Views

* Zniff sensor;
* packet stream;
* victim endpoint;
* network map;
* penetration-test report.

---

# Running the Presentation

The visualisation is a standalone HTML application.

For example:

```bash
python3 -m http.server 8000
```

Then open:

```text
http://127.0.0.1:8000/vanguard-keynote-demo.html
```

Presentation controls include:

```text
Space / Enter / →   Advance
A                   Auto mode
Z                   Zniff
N                   AttribTNG Navigator
M                   REHEARSE / RIG label
R                   Restart
```

---

# What This Project Is Not

This is not:

* an autonomous Internet attack bot;
* a recommendation to give LLMs unrestricted offensive access;
* proof that cyber attribution can be certain;
* a production exploitation framework;
* a substitute for human analyst judgement.

It is a controlled experiment in:

* AI-assisted penetration testing;
* evidence-based attack decisions;
* mainframe security;
* autonomous tool selection;
* defensive telemetry;
* ATT&CK/TTP correlation;
* threat attribution in noisy environments.

---

# Safety

SighberBank and GIBSON form a deliberately constructed security-training environment.

The techniques represented here should only be used:

* against systems you own;
* in purpose-built labs; or
* under explicit penetration-testing authorisation.

---

# Attribution Caveat

Threat actors frequently share techniques, tooling and infrastructure patterns.

Tools can be copied.

Infrastructure can be reused.

Behaviour can be imitated.

False flags exist.

Telemetry is incomplete.

AttribTNG therefore produces an **attribution hypothesis supported by observed evidence**, not a declaration of ground truth.

---

# Research Questions

This project explores questions including:

* Can an AI make meaningful penetration-testing decisions from incomplete evidence?
* Can it recognise failure and change approach?
* Can it decide not to attack an available system because it is irrelevant to the objective?
* Can penetration-testing actions be evidence-gated?
* What telemetry does an autonomous penetration test create?
* Can attribution operate effectively in the presence of unrelated traffic?
* What happens when AI-supported offence and automated attribution operate simultaneously?

---

# Disclaimer

This project is provided for **authorised security research, education and demonstration purposes only**.

---

## In One Paragraph

**A real AI was placed inside a controlled penetration-testing harness and given authorised security tools rather than a predetermined attack script. It made penetration-testing decisions from the evidence returned by those tools, creating real lab inputs, attacks, decision points and outputs. At the same time, Zniff observed the activity and AttribTNG independently correlated the resulting TTPs to form probabilistic attribution hypotheses, while a continuous background-noise generator introduced unrelated normal and hostile-looking traffic. This repository visualises that campaign for research, training and conference presentation.**


