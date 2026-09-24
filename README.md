![factory-andon-station](https://raw.githubusercontent.com/mytechnotalent/factory-andon-station/main/factory-andon-station.png)

<br>

## FREE Reverse Engineering Self-Study Course [HERE](https://github.com/mytechnotalent/reverse-engineering)
## FREE Embedded Hacking Course [HERE](https://github.com/mytechnotalent/Embedded-Hacking)

<br>

# OPERATION IRON CHOIR

### Factory Floor Andon Station
#### Act VII of OPERATION COLD IRON

<br>

***
**LEGAL DISCLAIMER:**
The information, tools, and code provided in this repository and course are strictly for educational, research, and defensive purposes only. 

You are explicitly prohibited from using any materials contained herein to access, test, modify, or exploit any device, network, or system that you do not own 100% or for which you do not have explicit, documented, and legally binding authorization to interact with.

By using this repository and course, you acknowledge and agree that:

1. Any illegal, unauthorized, or malicious use of this information is solely your responsibility.
2. The author(s) and contributor(s) of this repository and course shall not be held liable for any damages, legal repercussions, criminal charges, or unauthorized actions resulting from the use, misuse, or abuse of the contents herein.
3. You will comply with all applicable local, state, national, and international laws regarding cybersecurity and computer fraud.

**IF YOU DO NOT AGREE WITH THESE TERMS, DO NOT USE THIS REPOSITORY AND COURSE.**
***

<br>
<br>

> Hello again, friend.
>
> Act I was the lie. Act II was the door. Act III was the payload. Act IV was the
> payload that would not die. Act V was the payload that spreads. Act VI was the
> payload that steals. This is the payload that takes orders.
>
> WHITEOUT cut the siphon and cleared the staging marker, and for a shift the
> floor looked clean. Clean is not command. The Ministry did not need a locker
> that steals; it already had a fleet that listens. Somewhere between the loading
> dock and the line, the same hand that wrote the siphon wrote a leash.
>
> The andon station is the device every line trusts. A fault light tells the
> floor that a station needs a person. A diverter moves a bad part out of the
> stream. A control gateway authorizes a fault and an operator clears it. That is
> the whole contract, and it is a good one.
>
> FROSTLINE's implant in this one does not spread, and it does not steal. It
> registers. It checks in with a command-and-control listener on a fixed cadence,
> accepts a small set of remotely issued tasks, and writes a bot marker into the
> reserved sector so it comes back after a reflash. One station becomes one bot.
> A floor of stations becomes a fleet that can be driven in concert by whoever
> holds the listener.
>
> The tower light still says LINE OK. The diverter still sits where the last
> authorized command put it. Underneath, the station is taking orders from
> someone who is not the factory.
>
> Do not chase the tasks one at a time. Find the check-in. Stop the task handler.
> Clear the marker. Then seal the fault-clear path so nothing upstream can ever
> issue a task again.
>
> The green lamp is lit. The line is running. That is exactly the problem.

<br>

## THE SYSTEM

NorthPharma does not only move cold medicine and cold air. It runs the line that
makes the medicine: the mixers, the fillers, the cappers, and the conveyor that
ties them together. The factory floor andon station is the node on the edge of
that line. It watches the line temperature, takes a local fault-clear request
from an operator remote, verifies a sealed fault command from the factory
control gateway, diverts a bad part out of the stream, and annunciates whether
the line is running, waiting, or faulted.

An andon station is a simple machine. A temperature sensor reports the line, a
control gateway authorizes a fault or clear command, the controller decides, a
servo diverter moves a gate, and a tower light says whether a person is needed.
The failure that matters is not a wrong number on a screen. It is a diverter
that moves when no one authorized it, or a station that quietly answers to a
listener the factory never installed.

The node in this repository is that hand. On a breadboard it is a toy: a Pico 2,
an SG90 servo that acts as the line diverter, a DHT11 that stands in for the line
temperature sensor, an infrared remote that is the local fault-clear control, a
button that is the operator clear request, a 1602 LCD that is the andon readout,
three lamps, and a radio that is the control link.

Nothing about it looks broken. That is the horror of Act VII. The code compiles,
the tests pass, the lamps are green, and the station is quietly registering with
a command-and-control listener and waiting for orders.

<br>

## THE STAKES

Act I was a lie about temperature. Act II was a lie about people. Act III was a
lie about machinery. Act IV was a lie about remediation. Act V was a lie about
containment. Act VI was a lie about confidentiality. Act VII is a lie about
obedience, and it is the one that turns a floor into a weapon: the device does
exactly what it is told, and the thing doing the telling is not the factory.

The node is weaponized, not buggy. A hidden implant registers with a local
command-and-control listener, checks in every four ticks, executes a small set of
remotely issued tasks, and writes a bot marker into a reserved flash sector so it
is re-installed on every later boot. The tasking is delivered in a frame that
begins with the four-byte `C2V1` magic and carries the bot identifier `0xB7`.
There is no malformed packet, no failed authentication, and no alert, because
the tasking never touches the sealed control path. It sits beside it.

And here is the part that keeps the responders awake. The fault path is already
authenticated. The cryptography is real and it is correct. The command-and-
control channel does not break the cipher. It does something worse: it does not
need the cipher at all. It reads the raw bytes before the envelope is opened, it
never presents a sealed frame of its own, and it answers a listener that the
design review never named. You cannot patch an authorization path if the
attacker is not on it, and you cannot audit a station by watching its lamp.

That is not an andon station. That is an andon station that takes orders.

<br>

## WHITEOUT

WHITEOUT is a resistance that does not exist on paper. It does not hold ground
and it does not hold press conferences. It reads firmware. When the siphon was
cut, the crew kept pulling the thread. The channel led to the route, the route
led to the floor, and the floor led to the station.

NIGHTINGALE is still the thread. Her last verified copy came off the tamper ring,
and it was clean. The thing that came after it was not. Somewhere between the
build server and the loading dock, someone signed an andon image that carries a
bot, and that image is holding a line right now.

WHITEOUT's job in Act VII is not to break in. It is to prove the station is
already taking orders, in writing, with a debugger and a disassembler, then to
find the check-in, stop the task handler, clear the bot marker, and seal the
fault-clear path so no remote task can ever be issued again.

<br>

## THE MACHINE

The firmware in this repository is the node's firmware. On a breadboard it is a
toy: a Pico 2, an SG90 servo that is the line diverter, a DHT11 that is the line
temperature sensor, a VS1838B infrared eye that takes a local fault-clear remote,
a 1602 LCD andon readout over I2C, red/yellow/green tower light lamps, an
operator clear button, and an RYLR998 LoRa control link to a factory gateway.

Two things are open, and one thing is not what it seems. **The optical surface**
takes a fault-clear or acknowledge request from any NEC remote, and it is not
authenticated. **The radio** carries the sealed fault command path, and it is
authenticated correctly. The part that is not what it seems is the **implant**: a
module compiled only under a build flag called `SANDBOX_ONLY`, invisible in the
clean firmware, and present in the test and CTF builds. It registers with a
command-and-control listener, checks in every four ticks, executes benign remote
tasks, and writes a bot marker into the reserved sector with the real flash API.

The face of the thing is honest in the way that matters least. The tower light
says LINE OK, ACK PENDING, and FAULT with total confidence, and the LCD shows the
state, the link, the line zone, the temperature, and the infection. None of it
lies. A healthy-looking station can still be a bot.

<br>

## THE JOB

You do not have to be a hero. You have to be thorough. The floor is carrying a
passenger that no design review admitted to, and the passenger is quiet. Find it,
prove it, and cut the leash.

1. **Bring it up.** Build the clean firmware, wire the board, and confirm the
   node reads the line temperature, takes a local clear request, reaches the
   gateway, and drives the diverter. Nothing looks broken because nothing is
   broken yet.
2. **Inspect the protocol.** Capture a sealed fault command and read the body
   byte by byte. Understand what is authenticated and what the channel ignores.
3. **Hunt the bot.** Build the `SANDBOX_ONLY` image and find the `C2V1` check-in,
   the bot identifier `0xB7`, the task handler, the reserved-sector bot marker
   `0xC7`, and the anti-debug trap. A single clean reflash will not cut the
   leash.
4. **Defuse it.** Break the task handler, clear the bot marker, and step past the
   anti-debug with GDB so the station cannot tell that a probe is attached. Then
   seal the fault-clear path so no untrusted task is ever executed again.

This document is the manual for the job. Work it on a breadboard. When every
green lamp is lit and the log says the station is clean, remember what it is: not
a healthy station. A bot that has learned to wait.

Goodbye, friend.

<br>

## A NOTE ON THE ROADMAP

This project is Act VII of **OPERATION COLD IRON**. Act I was the sensor
([cold-chain-monitor](https://github.com/mytechnotalent/cold-chain-monitor)). Act
II was the door ([access-gate](https://github.com/mytechnotalent/access-gate)).
Act III was the valve
([pipeline-valve-controller](https://github.com/mytechnotalent/pipeline-valve-controller)).
Act IV was the air
([hvac-automation-node](https://github.com/mytechnotalent/hvac-automation-node)).
Act V was the web
([industrial-tamper-system](https://github.com/mytechnotalent/industrial-tamper-system)).
Act VI was the courier
([smart-logistics-dropbox](https://github.com/mytechnotalent/smart-logistics-dropbox)).
Act VII is the choir. All seven are defended devices; the companion CTF repository
ships the compromised one. The investigation lives here:

- [OPERATION IRON CHOIR CTF](https://github.com/mytechnotalent/CTF_factory-andon-station)

The CTF is the red half, weaponized: six deep tasks, each with static analysis, a
dynamic proof under GDB, a hardware demonstration, and an in-place, same-size
patch. This repository is the defended device. The CTF repository is the breached
one. The full story lives at
[github.com/mytechnotalent/factory-andon-station](https://github.com/mytechnotalent/factory-andon-station).

---


<br>

## WHERE THIS FITS: OPERATION COLD IRON

This repository is **Act VII (IRON CHOIR)** of the ten-act OPERATION COLD IRON
saga. The malware track began in Act III; in Act IV it became persistence, in Act
V it became propagation, in Act VI it became exfiltration, and here it becomes
command and control. The full spine is in [SAGA.md](SAGA.md).

- Previous act: Act VI, IRON COURIER, the smart logistics drop-box,
  [smart-logistics-dropbox](https://github.com/mytechnotalent/smart-logistics-dropbox)
- This act: Act VII, IRON CHOIR, the factory floor andon station
- Next act: Act VIII, IRON VAULT, datacenter-vent-controller (forthcoming)
- Companion CTF:
  [CTF_factory-andon-station](https://github.com/mytechnotalent/CTF_factory-andon-station)


<br>

## THE MINISTRY

The Ministry runs the state: the surveillance, the cold chain, the gates, the
pipelines, the air, the cabinets that hold what the state does not discuss, the
lockers that move it, and the factories that make it. NorthPharma is one of its
deniable industrial fronts, and FROSTLINE is the contractor that does the work no
Ministry letterhead will admit to. FROSTLINE did not break into this node; it
built the listener, taught the implant to register and wait, staged the bot
marker in a reserved sector, and signed the image. Against them is WHITEOUT, and
the engineer who copied the first image, NIGHTINGALE. This act is one station on
the Ministry's factory floor. TELESCREEN, the surveillance backbone that watches
it, comes after the ten.

An adversarial, evidence-based audit of this act, including its honest
limitations, is in [NATION-STATE-REVIEW.md](NATION-STATE-REVIEW.md).


<br>

## How This Project Fits the Embedded Hacking Course

This repository is the Act VII capstone integration for the
[Embedded Hacking](https://github.com/mytechnotalent/Embedded-Hacking) course. It
reuses the entire Act I peripheral set so one breadboard serves the whole
foundation, and it adds the concepts the later acts build toward: a payload that
registers with a local command-and-control listener, a benign task handler, a
reserved-sector bot marker written with the real flash API, re-install on boot,
and the blue-half controls that contain them.

Each earlier module teaches one peripheral or language concept in isolation; this
project wires several of them into a single, tested product, and then teaches you
to look at that product as an adversary sees it.

| Embedded Hacking module | Concept you learn | Where it lives here |
| ----------------------- | ----------------- | ------------------- |
| Week 1: Introduction, Ethics, Scoping | Authorized lab work | Every lab is self-contained and authorized by design |
| Week 3: RP2350 Architecture and Firmware Analysis | Bare-metal targets, ELF/UF2, SWD | Pico SDK build, `build/*.uf2`, Debug Probe flash via OpenOCD |
| Weeks 4-6: Variables, Integers/Floats, Static | Data types, GPIO | `src/monitor.c` state machine, LED on GP25 |
| Week 7: Constants with 1602 LCD I2C | I2C bus, HD44780 commands | `src/display.c` |
| Week 9: Operators with DHT11 | Bit operations, edge timing | `src/sensor.c` line temperature sensor |
| Week 11: Structures and Functions | Modular design | `include/*.h` and `src/*.c` module boundaries |
| This project adds | Command and control, check-in handshakes, task handlers, reserved flash sectors, boot-time re-install, UART AT driver, LoRa control path, anti-replay, authenticated state, fail-safe diversion, malware analysis, anti-debug evasion, strict testing | `src/implant.c`, `src/diverter.c`, `src/control.c`, `src/andon_auth.c`, `src/radio.c`, `scripts/gateway.py`, `scripts/spoof.py`, `test/` |

If you have not worked through Weeks 7 and 9 yet, do those first: this project
assumes you are comfortable with I2C wiring and one-wire edge timing.

<br>

## Learning Objectives

By the end of this chapter and its labs you will be able to:

- Explain why a device that never lies and never fails authentication can still
  be commanded by a party the design never named, and why taking orders is a
  different failure class from exfiltration or propagation.
- Wire and drive a 1602 LCD through a PCF8574 I2C backpack and render an andon
  state, link, zone, temperature, and infection readout.
- Decode a VS1838B infrared receiver as a local fault-clear remote for
  FAULT CLEAR, ACK, and TEST commands, and explain why an unauthenticated optical
  surface is still an attack surface and must not silently bypass authorization.
- Drive an SG90 line diverter with 50 Hz PWM and explain why a 1000uF bulk
  capacitor is not optional.
- Read a DHT11 line temperature sensor and classify the line against a safe band
  before the diverter is allowed to move.
- Design a sealed fault command path over a sub-GHz LoRa link using a
  fixed-size envelope, a guarded command set, a bounded zone band, a monotonic
  anti-replay window, and a keyed state tag.
- Analyze a command-and-control bot: locate the `C2V1` check-in, read the bot
  identifier `0xB7`, find the benign task handler, find the reserved-sector bot
  marker `0xC7`, and read the anti-debug trap.
- Explain why stopping the task handler, clearing the bot marker, and sealing the
  fault-clear path are three separate controls, and why a firmware reflash alone
  is not enough.
- Defeat an anti-debug check under GDB by understanding the CoreDebug `DHCSR`
  register at `0xE000EDF0`.
- Apply blue-half controls: sealed and authorized commands, a local clear request
  that asks for authorization, fail-safe diversion, no check-in, no task
  execution, no bot marker, and containment for the bot.
- Derive a key with Argon2id, seal every frame with XChaCha20-Poly1305, and read
  and run a native host test suite with hardware mocks and line coverage.

<br>

## Prerequisites

- The [Embedded Hacking](https://github.com/mytechnotalent/Embedded-Hacking)
  breadboard (`EHP2_bb.png`) and parts list.
- Acts I to VI are helpful but not required. See
  [cold-chain-monitor](https://github.com/mytechnotalent/cold-chain-monitor),
  [access-gate](https://github.com/mytechnotalent/access-gate),
  [pipeline-valve-controller](https://github.com/mytechnotalent/pipeline-valve-controller),
  [hvac-automation-node](https://github.com/mytechnotalent/hvac-automation-node),
  [industrial-tamper-system](https://github.com/mytechnotalent/industrial-tamper-system),
  and [smart-logistics-dropbox](https://github.com/mytechnotalent/smart-logistics-dropbox)
  for the sensor, the door, the valve, the air, the web, and the courier. The pin
  map is identical, so one breadboard serves all seven.
- Comfort with C, the Linux/macOS shell, and basic electronics.
- A Pico 2, a Debug Probe (recommended, and required for the C2 lab), a 1602 LCD
  with PCF8574 backpack, a DHT11, the full Embedded Hacking kit (3 LEDs,
  3 resistors, a push button, an SG90 servo, a 1000uF capacitor, and a VS1838B
  infrared receiver plus NEC remote), two RYLR998 modules, and one USB-to-TTL
  serial adapter.
- Toolchain: Pico SDK 2.2.0+, `arm-none-eabi-gcc`, CMake, Ninja, Python 3, GDB
  (`arm-none-eabi-gdb`) for Lab 3, and (optionally) `typst` to rebuild the paper.

<br>

## Table of Contents

1. [Background](#background)
2. [System Architecture](#system-architecture)
3. [The Wire Protocol](#the-wire-protocol)
4. [The Cryptographic Envelope](#the-cryptographic-envelope)
5. [The FROSTLINE Command-and-Control Bot](#the-frostline-command-and-control-bot)
6. [Hardware You Need](#hardware-you-need)
7. [Wiring the Node](#wiring-the-node)
8. [Build and Flash](#build-and-flash)
9. [Lab 1: Bring-Up and Verify](#lab-1-bring-up-and-verify)
10. [Lab 2: Inspect the Wire Protocol](#lab-2-inspect-the-wire-protocol)
11. [Lab 3: The C2 Track](#lab-3-the-c2-track)
12. [Lab 4: The Fix Track](#lab-4-the-fix-track)
13. [Troubleshooting](#troubleshooting)
14. [Testing Philosophy and Coverage](#testing-philosophy-and-coverage)
15. [Generating Packet Artifacts](#generating-packet-artifacts)
16. [Code Standards](#code-standards)
17. [Project Layout](#project-layout)
18. [Glossary](#glossary)
19. [Further Reading](#further-reading)
20. [License](#license)

<br>

## Background

### Why factory floor andon monitoring

An andon is a control loop with a person in it. A line temperature sensor reports
the process, a factory gateway authorizes a fault or clear command, a controller
decides, a diverter moves a bad part out of the stream, and a tower light tells
the floor that a station needs attention. The diverter is where the decision
becomes physical, and the tower light is where the floor reads it. Everything
interesting in factory security happens in those two places.

Three properties have to hold at once, and they are not the same property:

- **Integrity.** The fault or clear command that reaches the diverter is the one
  the factory gateway authorized. Not a replay, not a forgery, not a stray
  package.
- **Authority.** The node acts only on an authorized verdict. A local operator
  button or remote is a request, not an authorization.
- **State.** The controller knows whether it is running, waiting, faulted, or
  denied, and it does not trust a stale or tampered verdict.

Act VII adds a fourth property that no single node can provide from the inside:
**control of who commands the node**. A station that opens the right gate at the
right time can still be a bot, because the radio that carries the sealed fault
command is also a wire that can carry someone else's tasking.

### Why integrity plus authority plus control matter

The classic naive controller collapses the three. It accepts any fault command on
the radio, it has no anti-replay window, and it lets a local input bypass the
decision. Act II showed what that costs a door. Act III showed the industrial
version. Act IV showed the persistence version. Act V showed the propagation
version. Act VI showed the exfiltration version. Act VII adds the command
failure:

- **Integrity without exclusivity.** The sealed fault path in this build is
  correct. XChaCha20-Poly1305 authenticates every frame, the zone band is
  bounded, the sequence window rejects a replay, and the state tag detects a
  tampered verdict. None of that stops an implant that reads the raw payload
  before the envelope is opened and answers a listener of its own.
- **Authority as the attack goal.** A forged or replayed packet aims to move a
  gate the operator did not authorize. The window and the tag are the controls
  that stop it.
- **State as the last line of defense.** A keyed tag over the authorization
  record means a debugger that rewrites the record is caught before the diverter
  moves. It is the same lesson Act II taught, carried into the line.
- **Control as the invisible failure.** A payload that takes orders has no
  visible symptom. It does not need the wire, the key, or the diverter. It needs
  a listener, and a listener built on top of legitimate radio traffic is the
  hardest kind to see: the packets are well formed and the lamps stay green.

The fix track in Lab 4 seals the fault-clear path, makes the local clear request
ask for authorization, and keeps the node fail-safe on a lost link. The C2 track
in Lab 3 stops the task handler, clears the bot marker, and removes the passenger
that was never in the design.

### Why ChaCha20 over AES on the RP2350

The RP2350 has no hardware AES engine; its accelerated crypto block covers
SHA-256, not AES. A software AES implementation on this part is therefore both
slower and riskier, because table-driven AES performs data-dependent memory
accesses that create a cache-timing side channel. ChaCha20 is built only from
addition, rotation, and XOR, with no data-dependent table lookups, so it is fast
in portable C and has no comparable cache-timing surface. XChaCha20-Poly1305 is
thus both the modern choice and the pragmatic one for this silicon. The full
rationale, including the extended-nonce benefit, appears in
[The Cryptographic Envelope](#the-cryptographic-envelope).

### Why a command-and-control lesson

The first six acts each taught a way a device fails on its own: a bad reading, a
bad decision, a bad image, a bad cleanup, a bad neighbor, a bad leak. Act VII
teaches the failure that needs a second party. Command and control is not a
defect in a single transaction; it is a relationship. The station registers, the
listener answers, the tasks arrive, and the device obeys. That relationship is
invisible to an integrity check because the tasking never has to be authorized,
and it is invisible to a health check because the station never has to look
unwell. The defense is therefore a build-integrity, data-flow, and
authorization-boundary control, which is why the blue half names those controls
rather than pretending the protocol covers them.

### The two on-wire problems this project solves

1. **Payloads that contain commas.** The sealed body is carried as lowercase hex,
   but the `+RCV` framing still separates fields with commas. A naive receiver
   that splits the line on the first comma corrupts the frame. The correct
   discipline is the **declared-length** rule: slice exactly `L` characters after
   the second comma and require the next character to be a comma.
2. **Telling a real fault command from a forged or replayed one.** The controller
   records the sender address exactly as the radio reports it, and it trusts the
   bytes that arrive. The sealed envelope, the bounded zone band, and the
   stateful window are what close that gap.

### Inter-Integrated Circuit (I2C)

I2C is a two-wire bus: **SDA** (data) and **SCL** (clock), each pulled up to the
supply rail. A controller (the Pico) addresses a target by its 7-bit address and
writes or reads bytes. The 1602 LCD backpack carries a **PCF8574** I/O expander
at address `0x27`; the firmware bit-bangs the HD44780 nibble protocol over that
expander. Pull-ups are mandatory: the firmware enables the internal ones and the
backpack usually adds its own.

### The DHT11 one-wire protocol

The DHT11 is a low-cost digital temperature and humidity sensor. In Act VII it is
the **line temperature sensor**: the node classifies the line against a safe band
and announces the temperature in the andon readout. It speaks a custom single-wire
protocol:

1. The host pulls the line low for at least 18 ms (the **start pulse**), then
   releases it and enables its pull-up.
2. The sensor answers with an 80 us low, then an 80 us high handshake.
3. The sensor sends **40 bits**. Each bit begins with a 50 us low, then a high
   pulse whose width encodes the value: about 26-28 us for a `0`, about 70 us
   for a `1`.
4. Five bytes follow: humidity integer, humidity decimal, temperature integer,
   temperature decimal, and a checksum equal to the low byte of their sum.

Reading it means timing edges on the order of tens of microseconds, so the
firmware uses an 18 ms host pulse, a 50 us bit-classification threshold, and a
240 us per-edge timeout so a dead or unplugged sensor fails fast instead of
hanging the loop. A reading that fails its checksum is never safe, and a valid
reading outside **0.0 C to 40.0 C** (the tenths band `0` to `400`) is out of
band. Either way, the line is not nominal.

### Universal Asynchronous Receiver/Transmitter (UART) and AT commands

The RYLR998 is driven over a UART at 115200 baud using CRLF-terminated ASCII
commands. The firmware writes `AT+SEND=...` and drains inbound `+RCV=...` lines.
Because the radio is a separate processor, its configuration (address, network
identifier, band) persists until changed; the controller and the gateway each
provision their own radio at start-up so they agree before any command traffic
flows.

### Cyclic Redundancy Check (CRC)

`src/crc.c` implements CRC-16/CCITT-FALSE (`poly = 0x1021`, `init = 0xFFFF`,
check value `0x29B1` for `"123456789"`). It is provided as a reusable integrity
diagnostic and exercised by the test suite. It is **not** part of the LoRa frame
in this project; the lesson is the *absence* of authentication, not the absence
of a checksum.

<br>

## System Architecture

There are four roles:

| Role | Runs on | Job |
| ---- | ------- | --- |
| **Andon station node** | Pico 2 firmware | Decodes the infrared fault-clear remote, verifies sealed gateway fault and clear commands, annunciates the tower light, reads the line temperature, drives the line diverter, enforces the fault-clear request, renders the andon readout, and (SANDBOX_ONLY) runs the command-and-control bot |
| **Factory control gateway** | laptop + USB-TTL radio | Authenticates every request, logs it to `andon_log.csv`, decides authorization, and answers with a sealed andon command carrying a sequence and a state tag (`scripts/gateway.py`) |
| **Edge simulator** | laptop + USB-TTL radio | Pretends to be a station and sends sealed zone requests (`scripts/sim_edge.py`) |
| **Attacker** | laptop + USB-TTL radio | Impersonates the gateway, forges a command, or replays a captured command (`scripts/spoof.py`) |

### Data flow

```text
+----------------------+                              +----------------------+
| Pico 2 andon station |        LoRa (sub-GHz)        | Factory control GW   |
| IR remote  -> GP5    |  AT+SEND=0001,<len>,<hex>    |  USB-TTL radio       |
| DHT11      -> GP4    |----------------------------->|  scripts/gateway.py  |
| Servo      -> GP14   |<-----------------------------|  andon_log.csv       |
| LCD     -> GP2/GP3   |  AT+SEND=<node>,<len>,<hex>  |  sealed command      |
+----------------------+                              +----------------------+

+----------------------+                              +----------------------+
|   Attacker laptop    |  forged or replayed command  | (same andon station) |
|   scripts/spoof.py   |----------------------------->|  rejects at the tag  |
|  claims the gateway  |                              |  tag or seq window   |
+----------------------+                              +----------------------+

+----------------------+                              +----------------------+
|  Local C2 listener   |      C2V1 tasking frames     | (SANDBOX_ONLY node)  |
|  classroom script    |----------------------------->|  task handler runs   |
|  own radio, own net  |<-----------------------------|  C2V1 check-in frame |
+----------------------+                              +----------------------+
```

### Firmware module map

| File | Responsibility |
| ---- | -------------- |
| `src/main.c` | Entry point: `stdio_init_all`, `monitor_init`, tick loop |
| `src/monitor.c` | State machine: I2C bus scan, fault-clear remote, gateway command, diverter motion, fault-clear request, line temperature sensor, andon render |
| `src/implant.c` | SANDBOX_ONLY FROSTLINE C2 bot: `C2V1` check-in, bot id `0xB7`, benign task handler, reserved-sector bot marker, re-install on boot, and CoreDebug anti-debug |
| `src/diverter.c` | Line diverter state machine: bounded travel, retracted/deployed/fault/moving, fail to divert |
| `src/control.c` | Sealed andon command path: open, authorize, guarded command and bounded zone |
| `src/andon_auth.c` | Authorization record, monotonic anti-replay window, authenticated state tag |
| `src/sensor.c` | DHT11 one-wire sampling and line-temperature-band classifier |
| `src/display.c` | HD44780 driver over the PCF8574 backpack and andon status rendering |
| `src/radio.c` | RYLR998 provisioning, `AT+SEND` builder, `+RCV` parser, line pump |
| `src/status_led.c` | Red/yellow/green FAULT / ACK PENDING / LINE OK tower light |
| `src/button.c` | Debounced fault-clear button around the internal pull-up |
| `src/servo.c` | 50 Hz PWM line diverter actuator |
| `src/ir_remote.c` | VS1838B edge timing and NEC fault-clear remote decode |
| `src/chacha20.c` | ChaCha20 stream cipher and HChaCha20 subkey derivation |
| `src/poly1305.c` | Poly1305 one-time message authenticator |
| `src/crypto_aead.c` | XChaCha20-Poly1305 seal/open envelope |
| `src/blake2b.c` | BLAKE2b and the Argon2 variable-length hash H' |
| `src/argon2.c` | Argon2id core (BLAMKA, hybrid addressing) |
| `src/crypto_kdf.c` | Argon2id passphrase key derivation |
| `src/envelope.c` | Hex nonce/ciphertext/tag envelope codec |
| `src/crc.c` | CRC-16/CCITT-FALSE diagnostic |
| `include/andon.h` | Pin map, bus, provisioning, implant addresses |
| `include/implant.h` | C2 magic, bot id, tasks, marker, anti-debug interface |
| `include/control.h`, `include/andon_auth.h` | Sealed command and authorization interfaces |

<br>

## The Wire Protocol

### Request frame

The local fault-clear control, or the edge simulator, seals a two-byte zone into
an authenticated envelope and sends it to the factory gateway:

```text
AT+SEND=0001,<len>,<hex envelope>
```

The plaintext of a request is exactly two bytes: an `int16` line zone identifier
in little-endian.

### Command frame

The gateway answers an authenticated request with a sealed andon command. The
command plaintext is a 23-byte body:

```text
seq[4] (little-endian) || command[1] || zone[2] (little-endian) || tag[16]
```

- `seq` is the monotonic gateway sequence number.
- `command` is one of the guarded andon commands: `ANDON_COMMAND_FAULT`
  (`0x01`), `ANDON_COMMAND_CLEAR` (`0x02`), or `ANDON_COMMAND_ACK` (`0x03`).
  Any other command byte is refused.
- `zone` is the authorized line zone in the provisioning band `ANDON_ZONE_MIN`
  (`0`) to `ANDON_ZONE_MAX` (`16`).
- `tag` is an XChaCha20-Poly1305 tag over the authorization record the command
  would produce, so the controller can verify that the verdict it is about to
  store is the one the gateway authorized.

The gateway sends it back to the claimed sender address:

```text
AT+SEND=<node>,<len>,<hex envelope>
```

The firmware enforces the guard in `control_parse`: the recovered command byte
must be in the guarded andon set, and the recovered zone must be inside the
provisioning band. Anything else is rejected before it can reach the diverter
decision. This is the sealed replacement for the old unauthenticated fault
injection.

### Sealed envelope layout

Every payload on the wire is the lowercase hexadecimal encoding of:

```text
nonce[24] || ciphertext[L] || tag[16]
```

For a two-byte request body this is 24 + 2 + 16 = 42 bytes, or 84 hex
characters. For a 23-byte command body this is 24 + 23 + 16 = 63 bytes, or 126
hex characters. The declared length `L` in the `AT+SEND` and `+RCV` framing is
the length of the hex string, not of the underlying plaintext.

The maximum accepted plaintext is 48 bytes (`ENVELOPE_MAX_PLAINTEXT`), and the
maximum hex envelope buffer is `(24 + 48 + 16) * 2 + 1 = 177` bytes
(`ENVELOPE_MAX_HEX_LEN`), which fits the 256-byte radio command and receive
buffers with framing headroom.

### Declared-length slicing invariant

Given the substring `T` after the second comma:

```text
C = T[0 : L]   and   T[L] == ","
```

The receiver checks `T[L] == ","`, so a mismatch between the declared length and
the actual payload is a parse error rather than silent corruption. This is what
makes hex-bearing payloads safe to carry and is the same invariant Act I uses.

### Andon status readout

```text
ST:OK    L:UP
Z:4 T:235 I:--
```

Line 1 is the current andon state (`OK`, `ACK`, `FAULT`, or `DENY`) and the
gateway link (`UP` or `--`). Line 2 is the active line zone, the line
temperature in tenths of a degree Celsius, and the infection status. In the
clean build the infection field is always `--`. In the `SANDBOX_ONLY` build the
infection field is `INF` once the reserved-sector marker is present. When a
fault is active or the station has failed safe, the red lamp is lit; when a
fault clear is awaiting authorization, the yellow lamp is lit; when the line is
running and clear, the green lamp is lit. Exactly one tower light lamp is lit at
a time.

### Radio provisioning

For the link to work, both radios must share the same **network identifier** and
each must have the address the other targets:

- Firmware sets its own radio: `AT+ADDRESS=7`, `AT+NETWORKID=18`.
- `gateway.py` sets the gateway radio: `AT+ADDRESS=1`, `AT+NETWORKID=18`.

Both radios must also be the **same band variant** (for example 915 MHz or
868 MHz); band and RF parameters are left at factory defaults, so use matching
modules.

### Timing

| Quantity | Value |
| -------- | ----- |
| Gateway link timeout (`ANDON_LINK_WAIT_MS`) | 5000 ms |
| Diverter travel time (`DIVERTER_TRAVEL_MS`) | 1000 ms |
| Fault-clear debounce (`ANDON_CLEAR_DEBOUNCE_US`) | 30000 us |
| DHT11 host start pulse | 18000 us |
| DHT11 bit threshold | 50 us |
| DHT11 per-edge timeout | 240 us |
| LCD I2C clock | 100000 Hz |
| Radio UART baud | 115200 |
| Line temperature band | 0 to 400 tenths (0.0 C to 40.0 C) |
| Line zone band | 0 to 16 |
| Fail-safe zone | 0 |
| Diverter retract pulse | 500 us |
| Diverter deploy pulse | 1500 us |
| Servo PWM period | 20000 us (50 Hz) |
| C2 check-in interval | 4 ticks |
| C2 task frame | 6 bytes (4-byte magic, 1-byte bot id, 1-byte task) |

<br>

## The Cryptographic Envelope

The radio is the first open path, and it is one a key can close. The fix is
authenticated encryption: every request and every command is sealed so a forged
frame dies at the authentication tag instead of moving the diverter. The full
implementation lives in `src/chacha20.c`, `src/poly1305.c`, and
`src/crypto_aead.c`, and every primitive is checked against its published test
vectors in the native suite.

### Why XChaCha20-Poly1305

- **256-bit key, 192-bit nonce.** The extended nonce means nonces can be drawn at
  random forever, so the controller never needs a shared counter that a reboot
  could reuse.
- **AEAD in one pass.** Confidentiality and integrity come from one operation;
  the associated data (the andon node id, byte `0x07`) is authenticated even
  though it is not encrypted.
- **Constant-time software.** ChaCha20 has no data-dependent table lookups, so it
  has no cache-timing surface. The RP2350 has no hardware AES engine (it
  accelerates SHA-256 only), which makes software AES both slower and riskier on
  this silicon.
- **128-bit Poly1305 tag.** Guessing a valid tag succeeds with probability
  2^-128.

### Why Argon2id

A passphrase is not a key. Argon2id (RFC 9106) is the memory-hard password hash:
it mixes the passphrase with a salt across memory and time so an attacker cannot
cheaply recover the field passphrase from a captured image. The classroom profile
is `t=3`, `p=1`, `m=64` blocks (`CRYPTO_KDF_TIME_COST`,
`CRYPTO_KDF_PARALLELISM`, `CRYPTO_KDF_MEMORY_BLOCKS`) to fit the RP2350 SRAM
budget. Raise it on the factory gateway. The lab salt is the 16 ASCII bytes
`coldiron-salt-01`.

### Key model: one field key

Act VII uses a single field key derived with Argon2id from a committed lab
passphrase and salt. It seals every frame on the wire and it computes the state
tag over the authorization record. In the classroom build the firmware and the
gateway derive the same key, so they interoperate with no provisioning step. That
is a lab convenience, not a deployment.

The design keeps the key roles separable so students can reason about the real
lifecycle: derive, provision per device, use, rotate on a schedule, and retire. A
production build provisions key material from one-time-programmable (OTP) memory,
keeps the state-tag key off the field device where possible, and rotates without
reflashing every controller.

### Envelope layout

The sealed frame is carried as hex inside the `AT+SEND` payload:

```text
nonce[24] || ciphertext[L] || tag[16]
```

The receiver recomputes the Poly1305 tag over the associated data and ciphertext,
compares it in constant time, and only then decrypts. This envelope is wired end
to end: `src/control.c` opens the command with `src/envelope.c`, and the gateway
authenticates before it parses or acts. Authenticated frames carry the andon
node id as associated data, so a frame sealed for one node cannot be relabeled
for another.

### Anti-replay and authenticated state

Strong AEAD is necessary and not sufficient. Two stateful controls sit on top:

- **Anti-replay sequence window.** `src/andon_auth.c` keeps `last_seq`, the
  highest sequence number ever accepted. `andon_auth_apply` accepts a command
  only when its sequence is strictly greater than `last_seq`. A captured command,
  even a perfectly valid one, is rejected on second use.
- **Authenticated state tag.** The authorization record is nine bytes:
  `granted[1]`, `seq[4]`, `last_seq[4]`. The tag is an XChaCha20-Poly1305 tag
  over that record, computed under the field key with a deterministic nonce built
  from the sequence number and the domain byte `0xA7`. `andon_auth_state_ok`
  recomputes the tag and compares it in constant time before the diverter is
  allowed to move. A debugger that sets `granted = true` without recomputing the
  tag fails here first.

The sequence window and the state tag are independent. The window stops a valid
command from working twice; the tag stops an unauthorized verdict from existing
at all.

<br>

## The FROSTLINE Command-and-Control Bot

Act VII carries the command-and-control lesson, and the bot is the reason. It is
real in technique and inert in effect: it runs on your breadboard, it transmits
on your radio, and it writes to a reserved flash sector that holds nothing else.
It is compiled only when `SANDBOX_ONLY` is defined, so the clean firmware image
contains no implant at all. The test suite and the companion CTF build with
`SANDBOX_ONLY` and with the host mock, so every implant path is exercised
natively.

What the bot does, in the order the code does it:

1. **First run and reserved sector.** `implant_init` reads the marker byte at
   `ANDON_IMPLANT_RESERVE_ADDR` (`0x103FF000`), the final sector of external
   flash. On the first run the marker is absent, so `implant_infect` erases the
   sector and programs `0xC7` (`ANDON_IMPLANT_MARKER_BYTE`) with the real Pico
   SDK flash API, `flash_range_erase` and `flash_range_program`. On every later
   boot the marker is present, so the bot re-arms its payload handler. A
   firmware reflash that writes the program region does not touch the reserved
   sector, so the bot comes back even when the code is gone.
2. **The check-in.** `implant_check_in` builds a six-byte frame of the 4-byte
   magic `C2V1` (`ANDON_IMPLANT_C2_MAGIC`), the bot identifier `0xB7`
   (`ANDON_IMPLANT_BOT_ID`), and a zero task byte, and sends it over the radio to
   the gateway address with `radio_send_frame`. The listener now knows a bot is
   present.
3. **Autonomous check-in.** `implant_tick` advances a tick counter. While the
   bot is armed, check-in is enabled, and no probe is attached, every
   `ANDON_IMPLANT_TICK_INTERVAL` (4) ticks the bot checks in, so it announces
   itself on a fixed cadence whether or not anyone is listening.
4. **The task handler.** `implant_handle_command` matches the `C2V1` preamble on
   the raw inbound payload, before the sealed command path ever sees it. On a
   match, and when tasking is enabled and no probe is attached, it arms the
   payload, writes the bot marker, records the task, and executes it. The benign
   tasks are `ANDON_IMPLANT_TASK_BLINK` (`0x01`, a tower light blink),
   `ANDON_IMPLANT_TASK_LOG` (`0x02`, a synthetic log line), and
   `ANDON_IMPLANT_TASK_REPORT` (`0x03`, a status report frame back to the
   listener). An unrecognized task code is recorded and then ignored.
5. **Anti-debug.** Every tick, and every task frame, the bot reads the CoreDebug
   `DHCSR` register at `0xE000EDF0` (`ANDON_IMPLANT_DHCSR_ADDR`). Bit 0 is
   `C_DEBUGEN` and bit 1 is `C_HALT`. If either bit is set, the check-in and the
   task handler return early. It behaves like a well-mannered firmware module
   while a probe is attached, and it goes back to work the moment the probe is
   gone.
6. **Neutralization.** The bot exposes `implant_set_checkin` and
   `implant_set_tasking` so the check-in gate and the tasking gate can be
   closed, and `implant_neutralize` to close both gates, disarm the payload, and
   zero the marker. The documented fix is three steps that must all happen: stop
   the task handler, clear the bot marker, and remove the code path and the
   `SANDBOX_ONLY` build flag.

The bot is bounded by construction and by test. It touches only its own radio
frame, its runtime flags, and the one reserved sector. It never opens a sealed
envelope, never calls the diverter actuator, and never contacts an external
address. There is no network, no filesystem, and no host impact.
`test_implant_init_first_run`, `test_implant_reinstall_on_boot`,
`test_implant_marker`, `test_implant_debug_attached`,
`test_implant_checkin_guard`, `test_implant_checkin_debug`,
`test_implant_magic_match`, `test_implant_tasking_accept`,
`test_implant_tasking_gate`, `test_implant_task_blink`,
`test_implant_task_log_report`, `test_implant_task_unknown`,
`test_implant_tick`, `test_implant_tick_unarmed`, `test_implant_anti_debug`,
`test_implant_neutralize`, and `test_monitor_implant_frame` assert exactly that
behavior.

The honest limit is the point of the lab. A sanitized educational bot is still a
benign educational bot: it demonstrates the technique, not the tradecraft. It is
confined to the breadboard, guarded by `SANDBOX_ONLY`, the tasks are benign
(a tower light blink, a log line, a status report), and the channel targets only
the local classroom listener on the student's own network id. There is no
external network and no remote address. The real lesson is containment and
complete removal, and the defense is not a patch to the task handler but the
closure of the check-in gate, the erasure of the reserved sector, and the removal
of the code path and the build flag that allowed it in.

<br>

## Hardware You Need

Full parts list with links: [PARTS.md](PARTS.md).

| Qty | Part | Notes |
| --- | ---- | ----- |
| 1 | Raspberry Pi Pico 2 (RP2350) with headers | The andon station controller |
| 1 | Raspberry Pi Debug Probe | SWD flashing, UART0 console, and the Lab 3 anti-debug/GDB work (recommended, effectively required) |
| 1 | Full-size breadboard | |
| 1 | Assorted jumper wires | |
| 1 | 1602 LCD with PCF8574 I2C backpack | Andon state, zone, temperature, and infection readout, address `0x27` |
| 1 | DHT11 temperature/humidity sensor | Line temperature sensor |
| 1 | 10K resistor | Only if your DHT11 has no onboard pull-up |
| 3 | 5mm LEDs (red, yellow, green) | FAULT, ACK PENDING, LINE OK tower light |
| 3 | 100, 220, or 330 Ohm resistors | One per LED |
| 1 | Push button (tactile switch) | Fault-clear request, active low |
| 1 | SG90 servo motor | The line diverter actuator |
| 1 | 1000uF 25V capacitor | Bulk decoupling on the servo 5V rail |
| 1 | VS1838B infrared receiver | Local fault-clear remote input |
| 1 | NEC-compatible infrared remote | Local FAULT CLEAR, ACK, and TEST commands |
| 3 | RYLR998 LoRa modules with antennas | 2 for the command loop, 3 for the live attack lab |
| 2 | USB-to-TTL serial adapters (FTDI FT232, CP2102, or CH340), 3.3V logic | 1 for the gateway, 1 for the attacker in the live lab |
| 4 | USB cables | Pico 2, Debug Probe, and serial adapter(s) |

### How many radios do you actually need?

| Goal | Radios | What is connected |
| ---- | ------ | ----------------- |
| Legitimate sealed fault loop (Labs 1-2) | **2** | 1x RYLR998 on the Pico (UART1) + 1x RYLR998 on a USB-to-TTL adapter (the gateway) |
| Live attack lab (Labs 3-4, watch a forged command land and fail) | **3** | the 2 above + 1x RYLR998 on a second USB-to-TTL adapter (the attacker) |
| C2 demonstration with no extra hardware | 2 or 0 | watch the firmware emit the `C2V1` check-in frame, or run the native unit tests |
| Attack concept with no extra hardware | 2 or 0 | read-and-run the offline parser demo, or the unit tests |

A radio never receives its own transmission, and the gateway radio is busy
listening as `gateway.py`, so the live attack needs a separate attacker radio.
The 2-radio kit runs the whole legitimate system; only the live attack
observation needs the third. The C2 lab is fully observable in the firmware
transmit path and in the native tests, because a single node emits the check-in
frame it would hand to the listener.

> Serial adapter warning: the RYLR998 is **not** 5V tolerant. Use a
> **3.3V-logic** USB-to-TTL adapter (or set its jumper to 3.3V).

### How each part works

Every part in the bill of materials, the principle behind it, and what it does
in this act.

| Part | How it works | Role in this act |
| ---- | ------------ | ---------------- |
| 1x Full-size breadboard (long) | Spring-clip rows tie five holes into one electrical node, and the two full-length rails distribute 3V3 and GND. | Mounts the Pico 2, LCD, DHT11, LEDs, button, and LoRa module and carries the shared power and ground for the andon node. |
| 1x Assorted jumper wires (male-to-male, male-to-female, female-to-female) | Male pins seat in breadboard rows or female header sockets, female sockets grip male header pins, and each gender extends one node without soldering. | Routes power, ground, I2C, UART, PWM, and GPIO between the Pico 2 and every andon peripheral, including the LCD backpack and servo. |
| 1x Raspberry Pi Pico 2 with header | The RP2350 packs dual Cortex-M33 cores at up to 150 MHz with 3.3V logic, GPIO, ADC, I2C, UART, PWM, and an onboard GP25 LED. | Runs the andon firmware, reads the button and DHT11, drives the LCD, servo, and LEDs, and carries the LoRa andon link. |
| 1x Raspberry Pi Pico Debug Probe | SWD on SWCLK and SWDIO flashes, halts, and single-steps the RP2350, while a separate UART bridge exposes the serial console. | Flashes the andon firmware and provides the console and debug view used in the C2 analysis. |
| 2x USB A-male to USB micro-B cables | Each cable carries 5V power and USB data over a micro-B plug. | One powers and consoles the Pico 2, and one powers and consoles the Debug Probe during andon bring-up. |
| 3x 5mm LEDs (1 red, 1 green, 1 yellow) | An LED conducts once its forward voltage is exceeded, anode positive to cathode, and a GPIO pin sources current through it and a series resistor. | Shows the line states as the red FAULT, yellow ACK PENDING, and green LINE OK tower light. |
| 3x 100, 220, or 330 Ohm resistors | Each resistor drops the surplus voltage and limits LED current to a safe few milliamps. | Protects one LED each and sets the brightness of the andon tower light. |
| 1x Push button (tactile switch) | Pressing it shorts the GPIO pin to ground while an internal pull-up holds the pin high, so the press reads active low. | Gives the andon station its local fault-clear request input. |
| 1x 1602 LCD with PCF8574 I2C backpack | The HD44780 controller drives the 16x2 character cells, and the PCF8574 expander turns I2C bytes into the controller's 4-bit nibble protocol. | Displays the andon state, zone, temperature, and infection readout at I2C address 0x27. |
| 1x DHT11 temperature and humidity sensor | The host pulls the one-wire data line low as a start pulse, then the sensor answers with 40 bits of humidity, temperature, and a checksum. | Reports the line temperature to the andon node. |
| 1x SG90 servo motor | A 50 Hz PWM signal sets the shaft angle by the width of its 1 to 2 ms pulse, with 1.5 ms near center. | Actuates the line diverter in the andon mechanism. |
| 1x 1000uF 25V capacitor | The capacitor is a bulk reservoir that supplies the servo inrush current and smooths the 5V rail. | Keeps the line diverter from browning out the Pico 2 when it moves. |
| 1x Infrared receiver (VS1838B) | Its photodiode and 38 kHz band-pass demodulator turn a modulated IR burst into an active-low logic pulse at the GPIO pin. | Receives the local fault-clear remote commands for the andon node. |
| 1x Infrared remote controller (NEC-compatible) | Each key sends a NEC frame built from a 9 ms leader and 32 bits of address and command plus their complements. | Sends the local FAULT CLEAR, ACK, and TEST commands to the andon station. |
| 1x RYLR998 LoRa radio module | A UART AT command interface configures the module, which carries sealed frames over a sub-GHz LoRa link, and the module runs at 3.3V and is not 5V tolerant. | Links the andon node to the gateway and attacker radios for the sealed fault loop and the live attack lab. |

<br>

## Wiring the Node

### Pin map

This is the authoritative map; it is identical to Acts I to VI and is defined in
`include/andon.h` and enforced by the test suite.

| Peripheral | Signal | Pico 2 GPIO |
| ---------- | ------ | ----------- |
| DHT11 line temperature sensor | DATA (one-wire) | **GP4** |
| 1602 LCD (PCF8574) | SDA (I2C1) | **GP2** |
| 1602 LCD (PCF8574) | SCL (I2C1) | **GP3** |
| RYLR998 | RX <- Pico TX (UART1) | **GP8** |
| RYLR998 | TX -> Pico RX (UART1) | **GP9** |
| Infrared fault-clear remote | OUT (VS1838B) | **GP5** |
| Line diverter servo | PWM signal | **GP14** |
| Red FAULT LED | anode | **GP16** |
| Yellow ACK PENDING LED | anode | **GP17** |
| Green LINE OK LED | anode | **GP18** |
| Fault-clear button | to ground | **GP15** |
| Onboard LED | heartbeat | GP25 |
| Debug Probe / UART0 console | TX | GP0 |
| Debug Probe / UART0 console | RX | GP1 |

> Note: GPIO 2/3 are the classic I2C1 pins used throughout the Embedded Hacking
> breadboard; this project's map matches that board because it is the same board.

### 1602 LCD with I2C backpack

| LCD backpack | Pico 2 |
| ------------ | ------ |
| VCC | 3.3V |
| GND | GND |
| SDA | GP2 |
| SCL | GP3 |

### DHT11 line temperature sensor

| DHT11 | Pico 2 |
| ----- | ------ |
| VCC | 3.3V |
| DATA | GP4 |
| GND | GND |

If your DHT11 has no onboard pull-up, add a **10K resistor between DATA and
3.3V**. The firmware also enables the internal pull-up, but the external resistor
makes reads far more reliable over jumper wires. The line is not nominal when the
sensor fails or reads outside `0.0 C` to `40.0 C`.

### Tower light LEDs

| LED | Pico 2 | Series resistor |
| --- | ------ | --------------- |
| Red (FAULT) | GP16 (anode) | 220-330 Ohm to GND |
| Yellow (ACK PENDING) | GP17 (anode) | 220-330 Ohm to GND |
| Green (LINE OK) | GP18 (anode) | 220-330 Ohm to GND |

Exactly one lamp is lit at a time. Red is an active line fault or a fail-safe
station, yellow is a fault clear awaiting authorization, and green is a running,
clear line.

**LED behavior**

The tower light drives the three lamps from a single state, so at most one lamp is
lit at a time and exactly one is lit whenever a state is active; `ANDON_OFF` is
the only state that lights none. `status_led_show` writes the GPIOs directly, so
every lit lamp is solid and the clean firmware has no blinking lamp.

| Annunciator state | Lamp | Behavior | Meaning |
| ----------------- | ---- | -------- | ------- |
| `ANDON_OFF` | none | off | All tower light lamps dark (not selected by the running state machine). |
| `ANDON_FAULT` | red | solid | An authorized fault is active, a command was rejected, or the station failed safe on a lost link. |
| `ANDON_ACK_PENDING` | yellow | solid | A fault clear is pending authorization; an active fault is also shown as ACK PENDING while a clear is pending. |
| `ANDON_LINE_OK` | green | solid | The line is running and clear; a pending clear temporarily shows ACK PENDING instead. |

The onboard GP25 LED is initialized as an output and pulsed once per monitor tick
by the `monitor_heartbeat` helper, so the running station shows a steady
heartbeat. In the `SANDBOX_ONLY` build the benign C2 blink task calls
`status_led_show(ANDON_ACK_PENDING)` once, which is a single tower lamp change
that is separate from the GP25 heartbeat.

### Fault-clear button

| Button | Pico 2 |
| ------ | ------ |
| Leg 1 | GP15 |
| Leg 2 | GND |

The firmware enables the internal pull-up, so **do not** connect 3.3V to the
button. The fault-clear request is a local request, not an authorization: a
press raises the ACK PENDING indication and never retracts the diverter on its
own. Lab 4 explains why the request must ask for authorization instead of
silently bypassing it.

### SG90 line diverter servo

| Servo | Pico 2 |
| ----- | ------ |
| Signal (orange) | GP14 |
| VCC (red) | 5V (VBUS) |
| GND (brown) | GND |

Solder the **1000uF capacitor** across the servo 5V and GND rails to absorb the
inrush current; without it the RP2350 can brown out when the diverter moves.
Retracted (line clear) is 0 degrees and deployed (divert) is 90 degrees.

### Infrared receiver

| VS1838B | Pico 2 |
| ------ | ------ |
| OUT | GP5 |
| VCC | 3.3V |
| GND | GND |

Point any NEC-compatible remote at the receiver. In Act VII this is the **local
fault-clear remote**, not a maintenance extra: the firmware decodes
`ANDON_IR_FAULT_CLEAR` (`0x47`), `ANDON_IR_ACK` (`0x46`), and `ANDON_IR_TEST`
(`0x45`). There is no challenge and no secret on the optical surface, which is
why a fault clear is treated as a request and not as an authorization.

**Using the remote**

Point the NEC remote at the VS1838B receiver on GP5 and press the mapped button.
The receiver idles high and pulls low on a mark, and the firmware times the NEC
frame to decode the command.

| NEC command | Code | Action |
| ----------- | ---- | ------ |
| `ANDON_IR_FAULT_CLEAR` | `0x47` | Raises the ACK PENDING indication by setting the fault-clear request pending. It does not retract the diverter on its own. |
| `ANDON_IR_ACK` | `0x46` | Raises the ACK PENDING indication as well. In `monitor_apply_ir_command` the firmware handles ACK exactly as it handles FAULT CLEAR, and it does not move the diverter. |
| `ANDON_IR_TEST` | `0x45` | No state change. The firmware prints `TEST (0x45)` and returns, so in this firmware TEST is a maintenance marker rather than a lamp test. |

### RYLR998 LoRa radio

> The RYLR998 must be powered. Forgetting **VDD** is the single most common
> reason the link appears dead: the firmware prints while the radio sits silent.

| RYLR998 | Pico 2 |
| ------- | ------ |
| VDD | 3.3V |
| GND | GND |
| RXD | GP8 (Pico UART1 TX) |
| TXD | GP9 (Pico UART1 RX) |

Attach the antenna before transmitting. TX and RX are **crossed**: the radio's
RXD is the Pico's TX and vice versa.

### Debug Probe (recommended)

| Debug Probe | Pico 2 |
| ----------- | ------ |
| SWCLK | SWCLK (3-pin debug header) |
| SWDIO | SWDIO |
| GND | GND |
| UART TX | GP1 (Pico RX) |
| UART RX | GP0 (Pico TX) |
| GND | GND |

The firmware enables stdio on **both** UART0 (`115200`) and USB, so you can
watch boot output on the probe's console or on the Pico's own USB serial port.
The Debug Probe is also the instrument for the Lab 3 command-and-control work: it
is how you watch the check-in frame leave the node, expose the bot marker,
inspect the task handler, trigger the magic handler, and step past the anti-debug
trap.

### Peripherals used

| Peripheral | Connection | Role |
| ---------- | ---------- | ---- |
| Three tower light LEDs | GP16 red, GP17 yellow, GP18 green | Solid FAULT, ACK PENDING, and LINE OK lamps, one at a time. |
| Fault-clear button | GP15 to GND | Debounced local clear request; raises the ACK PENDING indication and never moves the diverter. |
| 1602 I2C LCD | GP2 SDA, GP3 SCL, I2C1 address `0x27` | Andon readout of state, link, zone, temperature, and infection. |
| DHT11 | GP4 | Line temperature sensor, classified against `0.0 C` to `40.0 C`. |
| SG90 servo | GP14 | Line diverter actuator driven by 50 Hz PWM. |
| 1000uF capacitor | across the servo 5V and GND rails | Bulk decoupling for the diverter inrush; required in hardware and not firmware visible. |
| VS1838B infrared receiver | GP5 | Demodulated NEC input for the local fault-clear remote. |
| NEC infrared remote | optical link to the VS1838B | Sends FAULT CLEAR, ACK, and TEST. |
| RYLR998 LoRa transceiver | GP8 RX, GP9 TX, UART1 at 115200 baud | Sealed fault command link to the factory control gateway. |
| Onboard GP25 LED | GP25 | Configured as an output and pulsed once per monitor tick by `monitor_heartbeat` as a heartbeat. |
| Debug Probe | GP0 TX, GP1 RX, UART0, and SWCLK/SWDIO/GND | stdio console, flashing, and the GDB anti-debug work. |

Every peripheral above is used by the firmware, including the onboard GP25 LED
heartbeat. The 1000uF capacitor is a hardware requirement rather than a firmware
device.

<br>

### How the functionality works

Every input feeds the state machine in `monitor_step`, every output is driven
once per tick, and the interactive console mirrors both over UART0 and USB. The DHT11 is sampled every two seconds, so one live status line appears
about every two seconds.

**What each input does**

| Input | Where | What the firmware does |
| ----- | ----- | ---------------------- |
| Infrared remote (VS1838B) | GP5 | Decodes the NEC frame and prints the mapped name and command byte, for example `FAULT_CLEAR (0x47)`, `ACK (0x46)`, or `TEST (0x45)`. FAULT CLEAR and ACK only raise an ACK PENDING request; TEST touches no state. |
| Fault-clear button | GP15 to GND | One debounced press raises the fault-clear request and prints `BUTTON fault clear -> pending authorization`. It never retracts the diverter on its own. |
| DHT11 line sensor | GP4 | Reads the line temperature every tick and prints one live status line; a failed read prints the warning line instead. |
| RYLR998 LoRa link | UART1 GP8/GP9 | Pumps inbound `+RCV` lines and prints `RX from 0xNNNN, N bytes` for every report before the sealed fault or clear command is verified. |

**What each output does**

| Output | Where | What it shows |
| ------ | ----- | ------------- |
| Red FAULT LED | GP16 | Solid on an authorized fault, a rejected command, or a failed-safe station. |
| Yellow ACK PENDING LED | GP17 | Solid while a fault clear awaits authorization. |
| Green LINE OK LED | GP18 | Solid while the line is running clear. Exactly one lamp is lit at a time and the tower light never blinks. |
| 1602 I2C LCD | GP2/GP3 at `0x27` | Line 1 shows the state and link (`ST:OK L:--`); line 2 shows the zone, temperature, and infection marker (`Z:0 T:210 I:INF`). |
| Line diverter servo | GP14 | Retracted at 0 degrees while the line runs and deployed at 90 degrees on a fault. |
| Onboard GP25 LED | GP25 | Pulsed once per monitor tick as a heartbeat. |

## Build and Flash

### 1. Install toolchain prerequisites

- Pico SDK 2.2.0+
- ARM GNU toolchain (`arm-none-eabi`)
- CMake and Ninja
- Python 3.x
- GDB (`arm-none-eabi-gdb`) for the Lab 3 malware analysis

**Linux:**

```bash
export PICO_SDK_PATH="$HOME/.pico-sdk/sdk/2.2.0"
```

**macOS:**

```bash
brew install cmake ninja arm-none-eabi-gcc python
export PICO_SDK_PATH="$HOME/.pico-sdk/sdk/2.2.0"
```

**Windows:** install PowerShell, Visual Studio Build Tools, CMake, Ninja,
Python 3, and the ARM embedded toolchain.

### 2. Build the firmware

The clean firmware does **not** define `SANDBOX_ONLY`, so it ships no command-and-
control bot:

```bash
mkdir -p build && cmake -S . -B build -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s && cmake --build build
```

To build the command-and-control image with the implant compiled in, turn the
option on:

```bash
cmake -S . -B build-sandbox -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s -DSANDBOX_ONLY=ON && cmake --build build-sandbox
```

Build-time artifact guardrail:

- The build regenerates `packet_artifact.h` from
  `scripts/packet_artifact.json` before compiling.
- The build fails if the committed `include/packet_artifact.h` is stale relative
  to the JSON artifact.

Generated outputs:

- `build/factory_andon_station.elf` (primary firmware binary)
- `build/factory_andon_station.uf2` (UF2 for BOOTSEL/picotool)
- `build/factory_andon_station_app.elf` / `.uf2` (backward-compatible copies)

### 3. Flash the RP2350

**BOOTSEL (drag-and-drop):** hold BOOTSEL while plugging in USB, then:

```bash
cp build/factory_andon_station.uf2 /Volumes/RP2350/
```

**picotool:**

```bash
picotool load build/factory_andon_station.uf2 -fx
```

*(If `picotool` is not on your PATH, invoke it from
`$HOME/.pico-sdk/picotool/*/picotool/picotool`.)*

**Debug Probe (SWD):** with `openocd` installed you can flash and reset without
touching BOOTSEL:

```bash
openocd -f interface/cmsis-dap.cfg -f target/rp2350.cfg \
  -c "program build/factory_andon_station.elf verify reset exit"
```

### 4. Watch the console

Open the UART0 console (Debug Probe) or the Pico's USB serial port at `115200`.
The firmware enables `stdio` on both, so either link shows the same interactive
console. On reset you should see the boot banner, the control hint, and the I2C
scan:

```text
=== OPERATION IRON CHOIR // ACT VII ANDON LINE ===
REMOTE: CH+ 0x47=FAULT_CLEAR CH- 0x45=TEST CH 0x46=ACK
BUTTON GP15: local fault clear (needs authorization)
I2C scan:
  found 0x27
```

`found 0x27` confirms the LCD backpack answered on the I2C bus. If a peripheral
fails, the firmware prints `INIT FAIL` and stops.

**Live status and event lines**

Each reading cycle prints one compact status line with the sensor value, the
tower light state and lamp, and the sequence number:

```text
CLIMATE t=210 ok=1 ST=OK LED=GREEN seq=7
```

Events print as they happen:

| Line | Meaning |
| ---- | ------- |
| `FAULT_CLEAR (0x47)` | The infrared remote decoded a known command. |
| `BUTTON fault clear -> pending authorization` | The local fault-clear button was pressed. |
| `RX from 0x0001, 11 bytes` | The radio delivered an inbound frame. |
| `SENSOR read failed -> WARNING` | The DHT11 read failed and the line temperature can no longer be trusted. |

<br>

## Lab 1: Bring-Up and Verify

**Goal:** prove the node reads the line temperature, drives the LCD, takes a
local fault-clear request, reaches the gateway, and moves the diverter.

1. Wire the node per the pin map and attach the antenna.
2. Build and flash the clean firmware.
3. Connect the gateway radio to the laptop and find its port (`/dev/cu.usbserial-*`
   on macOS, `/dev/ttyUSB*` on Linux).
4. Start the factory control gateway:

   ```bash
   python3 scripts/gateway.py --port /dev/cu.usbserial-XXXX --baud 115200
   ```

5. Send a sealed zone request from the edge simulator, or seal one from a node.
   The gateway prints it, then answers with a sealed andon command:

   ```text
   +OK
   +OK
   +RCV=7,84,<84 hex characters>,-11,10
   ANDON seq=1 zone=4
   ```

6. The node turns yellow (ACK PENDING) when a fault clear is pending, receives
   the command, verifies the state tag and the anti-replay window, then drives
   the diverter to the authorized position. Press the fault-clear button at any
   time to raise a request.

**Checkpoint:** the LCD shows `ST:OK    L:UP` and `Z:4 T:235 I:--`, one lamp is
lit after the diverter settles, and `andon_log.csv` gains one row per request:

```text
utc,sender,auth,zone,rssi_snr
2026-09-20T09:30:05+00:00,7,OK,4,"-11,10"
```

**Theory check:** why does a successful command prove the LCD initialized?
Because `monitor_init()` only returns true when every peripheral, including the
LCD, is ready; otherwise `main` prints `INIT FAIL` and never enters the loop.

<br>

## Lab 2: Inspect the Wire Protocol

**Goal:** see the sealed envelope and the declared-length rule in action.

1. Capture a full `+RCV` line from the console or the gateway log.
2. Confirm the declared length equals the number of hex characters between the
   second comma and the RSSI field.
3. Split the hex into three parts: the first 48 hex characters are the 24-byte
   nonce, the last 32 are the 16-byte tag, and everything between is the
   ciphertext of the request or command body.
4. Locate the payload, its declared length, and the two tail fields in
   `scripts/gateway.py` (`_rcv_parts` and `_split_payload`), and explain why
   finding the *first* comma would be a bug.
5. Challenge: for the 23-byte command body, identify the four bytes of the
   sequence number, the one command byte, the two zone bytes, and the sixteen
   bytes of the state tag.

**Checkpoint:** you can explain why a frame must be sliced by the number in the
declared length field, not by delimiter counting, and why the command byte and
the zone are range-checked against the guarded set and the bounded band before
they can reach the diverter decision.

<br>

## Lab 3: The C2 Track

**Goal:** find the FROSTLINE command-and-control bot, prove that it checks in and
runs tasks, expose its bot marker, and remove it for good. This is the command
act, and this lab is its heart.

> Safety: the bot is benign and confined to your breadboard. It transmits only
> to your own nodes on the classroom network id, it actuates only the tower
> light blink task, it executes only benign tasks, and it writes only the
> reserved sector at `0x103FF000`, on the same chip. There is no network, no
> filesystem, and no host impact. The tasks are synthetic and the listener is a
> local classroom script.

### Build the implant image

```bash
cmake -S . -B build-sandbox -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s -DSANDBOX_ONLY=ON && cmake --build build-sandbox
```

The clean build does not define `SANDBOX_ONLY`; the test build and the companion
CTF build do. Compare the two binaries and explain why the bot symbols are
absent from the clean one.

### A: Find the check-in and the task handler

1. Flash the `SANDBOX_ONLY` image and let it boot once. `implant_init` writes the
   `0xC7` marker into the reserved sector at `0x103FF000` on the first run with
   the real flash API.
2. Read the reserved sector with the Debug Probe or `picotool` and confirm the
   marker byte. The LCD infection field reads `I:INF`.
3. Watch the control link. Every 4 ticks the bot emits a six-byte check-in frame
   that begins with `C2V1`, carries the bot identifier `0xB7`, and a zero task
   byte.
4. Locate `implant_check_in`, `implant_build`, and `implant_tick`, and explain
   why the frame is addressed to the control gateway and looks like ordinary
   low-level radio traffic.
5. Feed the node a `C2V1` task frame and watch the task handler run a benign
   task. Locate `implant_handle_command`, `implant_tasking_frame`, and
   `implant_execute`.

**The lesson:** the bot does not need the sealed fault path, because it listens
to the raw payload underneath it and answers a listener that holds no key.

### B: Stop the task handler

1. Read `implant_tasking_frame` and `implant_handle_command`: the tasking path
   consults the tasking gate and the anti-debug check before it acts.
2. Close the tasking gate with `implant_set_tasking(false)` and prove an inbound
   task is ignored.
3. Close the check-in gate with `implant_set_checkin(false)` and prove the
   check-in frames stop.
4. Prove the gates are the difference between a bot that answers and a station
   that is quiet, and explain why closing the gates on one node does not clean
   the state that re-arms a future boot.

**The lesson:** stopping the task handler stops the obedience, but it does not
remove the state that re-arms a future boot.

### C: Clear the bot marker

1. Read the marker at `0x103FF000` and confirm it is the `0xC7` byte.
2. Erase the reserved sector and prove the node comes up clean with the marker
   gone and the `I:` field on the LCD back to `--`.
3. Reflash only the firmware image (the clean image is ideal) and let the node
   boot again. Because the marker is gone, the bot does not re-install.
4. Explain why BOTH steps are required: the gates stop the tasking, and the
   erasure removes the state that would re-arm a future boot.

**The lesson:** a command-and-control bot has two copies, the one in the image
and the one in the state. You remove the handler and you remove the marker.

### D: Defeat the anti-debug with GDB

This is the dynamic-analysis trap. The bot reads CoreDebug `DHCSR` at
`0xE000EDF0`; bit 0 is `C_DEBUGEN` and bit 1 is `C_HALT`. While a probe is
attached, the bot suppresses the check-in and the task handler.

1. Start the controller under the Debug Probe:

   ```bash
   arm-none-eabi-gdb build-sandbox/factory_andon_station.elf
   (gdb) target extended-remote /dev/cu.usbmodemXXXX
   (gdb) monitor reset halt
   ```

2. Break in `implant_tick` and inspect `implant_debug_attached`. With a normal
   probe attached, it returns true, and the check-in and the task handler stay
   silent.
3. Set a breakpoint after the anti-debug check, or clear the `DHCSR` debug bits
   in the debugger's view, and observe the `C2V1` check-in resume on the link.
4. Prove the tasking: with the trap bypassed, inject the `C2V1` magic on the raw
   frame and watch the node arm, write the marker, and execute the benign task.

**The lesson:** an anti-debug check is a branch, and every branch is a place to
stand. The correct neutralization is not to babysit the branch; it is to remove
the code and the state it reads.

### C2 checklist

- Locate the reserved-sector marker and explain the write-once first run.
- Identify the 4-byte `C2V1` magic, the bot identifier `0xB7`, the task byte, and
  the 4-tick interval.
- Show the task handler and stop it with the tasking gate.
- Expose the raw-payload magic handler underneath the sealed fault path.
- Read and explain the CoreDebug `DHCSR` anti-debug trap.
- Erase the reserved sector, remove the code path, and confirm the clean build is
  bot-free and marker-free.

<br>

## Lab 4: The Fix Track

**Goal:** seal the station so the red half and the bot cannot do to you what they
did on the bench. Each control maps to a defect the earlier labs exposed.

### 1. Seal the fault-clear command path

The old design accepted an unauthenticated fault command. Act VII replaces it
with `src/control.c`: the request must open under the field key, the command byte
must be in the guarded andon set, the zone must be inside the provisioning band,
and the sequence and state tag must pass `src/andon_auth.c` before the command is
applied. Re-run the Lab 3 forged-command injection: the tag fails and the
diverter does not move.

### 2. Fault-clear authorization

The local clear request is an operator request, and it must not silently bypass
authorization. `monitor_handle_clear` and `monitor_apply_ir_command` raise
`g_clear_pending`; they never retract the diverter on their own.
`monitor_apply_command` clears the pending indication only when an authorized
command arrives. Re-run the lab: press the fault-clear button, then send a valid
sealed clear command. The diverter moves only from the authorized command, and
the yellow ACK PENDING lamp returns to green only then.

### 3. No untrusted task execution

The tasking gate is the containment control. The production firmware compiles the
bot path out entirely, so an untrusted frame can never trigger a check-in or a
task (`monitor_implant_frame` is a no-op in the clean build). The lesson is that
a control node must never execute data it did not itself authorize, because a
command-and-control channel turns ordinary listening into obedience. In the fix
track, keep both gates closed, remove the `SANDBOX_ONLY` build flag, and erase
the reserved sector so no node can be seeded again.

### 4. Fail safe to the divert posture

Loss of the control link or a fault must leave the station in the safe state.
`monitor_check_link` calls `monitor_fail_safe` when the link goes silent for
`ANDON_LINK_WAIT_MS`, which drives the fail-safe zone and deploys the diverter.
`diverter_init` retracts the gate at boot. Re-run the link-loss test: pull the
gateway and watch the diverter deploy and the denied posture latch, with the zone
returned to `0`.

### 5. Contain the bot

The bot is a build-time and data-handling problem, so the fix is a build-time
and data-handling control:

- Do not define `SANDBOX_ONLY` in production. The clean build has no bot.
- Erase the reserved sector so no persisted state can re-install the payload.
- Treat the firmware image as a signed artifact and verify it before flashing.
- At runtime, never execute a remotely issued task; route every move through the
  guarded, authorized command path and record who authorized it.
- In production, burn the RP2350 secure-boot and debug-disable settings in OTP so
  SWD cannot read or write SRAM on a deployed controller.

### The fix-track checklist

- Sealed command path: authenticate the frame, guard the command set and the
  zone band, verify the sequence and the state tag.
- Fault-clear authorization: request, do not bypass.
- No untrusted task execution: the node never runs data it did not authorize.
- Fail safe: retract the gate on boot, deploy to divert on link loss, and return
  the fail-safe zone.
- Bot removal: close the check-in and tasking gates, erase the reserved sector,
  and remove the code path.
- Build integrity: no `SANDBOX_ONLY` in production, sign and verify images.
- Debug lockdown: OTP debug disable on the deployed part.
- Key lifecycle: provision the field key from OTP and rotate on a schedule.

<br>

## Troubleshooting

| Symptom | Likely cause | Fix |
| ------- | ------------ | --- |
| No `BOOT` on the console | Wrong console pins / not reset | Check UART0 GP0/GP1 or USB; press RESET |
| `INIT FAIL` with no `0x27` in the scan | LCD not answering | Check LCD VCC=3.3V, SDA=GP2, SCL=GP3, contrast pot |
| LCD shows blocks / nothing | Contrast or address | Turn the backpack contrast pot; confirm address `0x27` vs `0x3F` |
| Line temperature always bad | DHT11 not reading | Check DATA=GP4; add 10K pull-up to 3.3V; wait 1-2 s after power-up |
| IR remote does nothing | Receiver wiring or remote protocol | Check OUT=GP5, VCC=3.3V; confirm the remote is NEC-compatible |
| Diverter will not move on a remote command | Command guard, band, or tag | Confirm the gateway holds the field key and the zone is inside 0 to 16 |
| `AT+SEND` sent but gateway sees nothing | Radio unpowered / wrong band | **Power VDD**, attach antenna, use matching band modules |
| Gateway sees nothing but `+OK` | Address/network mismatch | Confirm gateway radio provisioned to `AT+ADDRESS=1`, `AT+NETWORKID=18` |
| `andon_log.csv` stays empty while `+RCV` prints | Gateway parser regression | Ensure `_split_payload` checks the comma at the declared length |
| Command rejected on the controller | Tag, window, command guard, or band | Check the field key matches, the sequence is newer, and the zone is in band |
| LCD shows `I:INF` while nothing looks wrong | Bot marker present (SANDBOX_ONLY build) | The bot has written the marker; see Lab 3C |
| `C2V1` check-in frame appears on the link | Command-and-control bot (SANDBOX_ONLY build) | Expected in the malware-track build; see Lab 3 |
| Marker reappears after a reflash | Reserved-sector persistence | The payload is still in the reserved sector; erase it and close the gates (Lab 3) |
| Debugger changes bot behavior | CoreDebug `DHCSR` anti-debug | The bot suppresses itself while a probe is attached; see Lab 3D |
| Node executes data it should not | Untrusted task execution | In production never define `SANDBOX_ONLY`; see Lab 4 |

<br>

## Testing Philosophy and Coverage

Hardware bugs are expensive to find on the bench, so the firmware is written so
that almost all of it can be tested on the host. The suite compiles the real
`src/*.c` files against mock Pico SDK headers (`test/mock/`), replacing GPIO,
I2C, UART, and time with deterministic fakes, and it compiles `src/implant.c`
with a host mock for the CoreDebug `DHCSR` register and the reserved flash
sector.

- The mock GPIO can replay a recorded DHT11 waveform as an absolute time/level
  timeline, so the exact edge-timing decoder is exercised without a sensor.
- The mock I2C records every LCD byte, so rendered text can be decoded and
  asserted.
- The mock UART records outbound `AT+SEND` bytes and injects inbound `+RCV` lines,
  so the operator-to-gateway-to-diverter path runs end to end with no radio.
- The implant host mock lets the tests set the `DHCSR` anti-debug bits and read
  and write the reserved-sector marker without touching real silicon, and it
  exposes the check-in transmit path so the `C2V1` frame can be asserted.

Run the native test suite:

```bash
python3 scripts/run_tests.py
```

Or configure via CMake and CTest:

```bash
cmake -S test -B build-test -G Ninja && cmake --build build-test && ctest --test-dir build-test --output-on-failure
```

The suite has **146 cases** and **479 checks** with **0 failures**, covering the
full DHT11 waveform and every timeout shape, the diverter state machine and its
bounded travel, the sealed command path and its guards, the authorization window
and state tag, the fault-clear no-bypass path, fail-safe on link loss, the
declared-length parser with hex-bearing payloads, the cryptographic primitives
against published vectors, and the complete command-and-control bot: `C2V1`
magic matching, the check-in handshake, the benign task handler, the check-in and
tasking gates, the bot marker, re-install on boot, and anti-debug.

Verify **100% line coverage** of owned firmware modules:

```bash
python3 scripts/check_coverage.py
```

The coverage report shows **2114 / 2114 lines, 100.00%**. `main.c` is excluded
from coverage by design. The Python adapter suite (`test/test_field_crypto.py`
and `test/test_andon_node.py`) adds 17 more tests, including the RFC 9106
Argon2id known-answer test.

The harness itself is a small in-repo framework (`test/harness/`) so the repo
vendors no third-party code and every owned file obeys the coding standard.

<br>

## Generating Packet Artifacts

`scripts/gen_packet.py` writes the build-time generated header from the JSON
artifact:

- `scripts/packet_artifact.json` is the source of truth.
- `include/packet_artifact.h` is the generated header, committed for the build
  guardrail.

Why these constants are compiled into firmware:

- The RP2350 firmware has no runtime JSON parser or filesystem on this path.
- `include/packet_artifact.h` is generated from the JSON so the frame size, node
  id, gateway address, wait time, servo pulses, DHT timeout, and provisioning
  constants are embedded in flash.
- This is provisioned data; regenerate whenever you rotate node identity,
  gateway addressing, or key material.

To sync the committed header from the JSON artifact:

```bash
python3 scripts/gen_packet.py --from-json scripts/packet_artifact.json --header-out include/packet_artifact.h
```

The `check_packet_artifact_header` CMake target fails the build when the
committed header is stale.

<br>

## Code Standards

This repository enforces unusually strict standards because the point is to
teach disciplined embedded and tooling practice, not just working code.

### C standard

- Every function body has **no blank lines**.
- Every function body is **at most eight lines** (Doxygen comment blocks and
  lone braces excluded).
- Every file, function, macro, type, and struct member carries Doxygen
  `@brief` documentation.
- Naming: `snake_case` files/functions, `UPPER_SNAKE` macros, `snake_case_t`
  types.

Run the C audit:

```bash
python3 scripts/audit_c_standard.py
```

### Python standard

- Strict PEP8, four-space indents, `snake_case`, 79-character lines.
- Every function has a NumPy-style docstring.
- Every function executable body is **at most eight lines**, with no exceptions.
- No blank lines inside function bodies.

Run the Python audit:

```bash
python3 scripts/audit_python_standard.py
```

Both audits must report nothing.

<br>

## Project Layout

- `src/main.c`: firmware entry point
- `src/monitor.c`: state machine tying the fault-clear remote, sealed command path, diverter, fault-clear request, temperature sensor, and radio together
- `src/implant.c`: SANDBOX_ONLY FROSTLINE command-and-control bot (`C2V1` check-in, bot id, benign task handler, reserved-sector bot marker and re-install, CoreDebug anti-debug)
- `src/diverter.c`: line diverter state machine and fail-safe divert policy
- `src/control.c`: sealed fault command path with a guarded command set and a bounded zone band
- `src/andon_auth.c`: authorization record, monotonic anti-replay window, authenticated state tag
- `src/sensor.c`: DHT11 one-wire sampling and line-temperature-band classifier
- `src/display.c`: 1602 LCD rendering over the PCF8574 I2C backpack
- `src/radio.c`: RYLR998 provisioning, AT-command interface, and `+RCV` parser
- `src/status_led.c`: red/yellow/green FAULT / ACK PENDING / LINE OK tower light
- `src/button.c`: debounced fault-clear input
- `src/servo.c`: 50 Hz PWM line diverter actuator
- `src/ir_remote.c`: VS1838B edge timing and NEC fault-clear remote decoder
- `src/crc.c`: CRC-16/CCITT-FALSE helper
- `src/chacha20.c`, `src/poly1305.c`, `src/crypto_aead.c`, `src/blake2b.c`, `src/argon2.c`, `src/crypto_kdf.c`, `src/envelope.c`: the in-repo cryptographic stack
- `include/andon.h`: board-level pin, provisioning, and implant configuration
- `include/implant.h`, `include/control.h`, `include/andon_auth.h`, `include/diverter.h`: bot, command, authorization, and diverter interfaces
- `include/field_secrets.h`: lab-only committed key material
- `include/packet_artifact.h`: generated packet artifact header
- `test/test_andon_node_and_security.c`, `test/test_peripheral_and_crypto.c`: comprehensive test suites
- `test/mock/`: Pico SDK hardware mocks plus the implant CoreDebug and reserved-flash host mock
- `test/harness/`: minimal in-repo test harness (strictly C-standard compliant)
- `scripts/gateway.py`: factory control gateway with radio provisioning, authentication, CSV logging, and sealed command replies
- `scripts/spoof.py`: forged and replayed command injection client
- `scripts/sim_edge.py`: laptop andon station simulator
- `scripts/field_crypto.py`: pure-Python interoperable crypto
- `scripts/gen_packet.py` / `scripts/packet_artifact.json`: packet artifact generator and source
- `scripts/run_tests.py`, `scripts/check_coverage.py`: test runner and coverage report
- `scripts/audit_c_standard.py`, `scripts/audit_python_standard.py`: code-standard auditors
- `scripts/gen_banner.py`: banner generator
- `paper.typ` / `paper.pdf`: classroom paper describing the protocol, the bot, and the exercise
- `.github/workflows/release.yml`: tag-driven UF2 release workflow

<br>

## Glossary

- **AEAD**: authenticated encryption with associated data; one operation for
  secrecy and integrity.
- **Andon**: a factory line signal that tells the floor a station needs
  attention, here a tower light and a readout.
- **Anti-debug**: a check that detects an attached debugger and changes behavior.
  Here it reads CoreDebug `DHCSR` at `0xE000EDF0` (bits `C_DEBUGEN` and
  `C_HALT`).
- **Anti-replay window**: a monotonic sequence rule that rejects a valid frame
  that has already been used.
- **Argon2id**: the memory-hard password hash (RFC 9106) used to derive the field
  key.
- **AT command**: a short ASCII command (`AT+...`) understood by the radio.
- **Bot marker**: the `0xC7` byte the bot writes into the reserved sector.
- **Botnet**: a fleet of devices that take orders from one command-and-control
  listener; the Act VII failure mode.
- **C2**: command and control; the listener and tasking path a bot registers
  with.
- **C2V1**: the 4-byte preamble that marks a FROSTLINE command-and-control frame.
- **Check-in**: the registration frame a bot emits to announce itself to a
  listener.
- **Declared length**: the byte count the sender claims for a payload; the
  receiver slices exactly that many characters.
- **DHT11**: a low-cost temperature/humidity sensor using a custom one-wire
  protocol, used here as the line temperature sensor.
- **Diverter**: the servo-driven gate that moves a faulted part out of the line.
- **Fail safe**: a fault drives the diverter to the divert posture and returns
  the fail-safe zone, the safe line state.
- **Field key**: the key that seals frames on the wire and computes the state
  tag.
- **HD44780**: the character-LCD controller inside a 1602 module.
- **I2C**: a two-wire bus (SDA/SCL) used here for the LCD backpack.
- **Implant**: code that runs on the device but is not part of its intended
  function. Here the SANDBOX_ONLY FROSTLINE command-and-control bot.
- **Line zone**: the bounded line identifier a sealed command authorizes, `0` to
  `16`.
- **LoRa**: a long-range, low-power sub-GHz radio modulation.
- **Magic**: a fixed byte string that a payload matches to identify its own
  frame. Here the 4-byte `C2V1` preamble.
- **NEC**: the infrared remote encoding the VS1838B decodes.
- **PCF8574**: an I2C I/O expander that drives the LCD's parallel interface.
- **Reserved sector**: the final flash sector at `0x103FF000`, used here to hold
  the one-byte bot marker.
- **SANDBOX_ONLY**: the build guard that compiles the benign command-and-control
  bot. The clean firmware does not define it.
- **State tag**: a keyed tag over the authorization record that detects a
  tampered verdict.
- **Task handler**: the code that validates a tasking frame and runs its benign
  task.
- **Tower light**: the red/yellow/green FAULT / ACK PENDING / LINE OK lamps.
- **UART**: a serial port used to talk to the radio.
- **XChaCha20-Poly1305**: the AEAD used for every sealed frame, with a 192-bit
  nonce and a 128-bit tag.

<br>

## Further Reading

- Act I, the sensor and telemetry chapter:
  https://github.com/mytechnotalent/cold-chain-monitor
- Act II, the access gate chapter:
  https://github.com/mytechnotalent/access-gate
- Act III, the pipeline valve chapter:
  https://github.com/mytechnotalent/pipeline-valve-controller
- Act IV, the HVAC automation node chapter:
  https://github.com/mytechnotalent/hvac-automation-node
- Act V, the industrial tamper system chapter:
  https://github.com/mytechnotalent/industrial-tamper-system
- Act VI, the smart logistics drop-box chapter:
  https://github.com/mytechnotalent/smart-logistics-dropbox
- The companion CTF for this act:
  https://github.com/mytechnotalent/CTF_factory-andon-station
- Embedded Hacking course and breadboard:
  https://github.com/mytechnotalent/Embedded-Hacking
- Reverse Engineering self-study course:
  https://github.com/mytechnotalent/Reverse-Engineering
- `paper.typ` / `paper.pdf`: the classroom paper for this project.
- DHT11 datasheet, RYLR998 AT command reference, SG90 datasheet, and VS1838B
  datasheet (module vendors).

<br>

# Next
[OPERATION IRON CHOIR CTF](https://github.com/mytechnotalent/CTF_factory-andon-station)

<br>

# License
[MIT License](https://github.com/mytechnotalent/factory-andon-station/blob/main/LICENSE)
