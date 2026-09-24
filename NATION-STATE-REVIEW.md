# OPERATION IRON CHOIR - Nation-State Accuracy Review

**An adversarial, evidence-based audit of the entire project where every claim is
verified by a re-runnable command or explicitly labelled as a limitation.**

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

## 1. Scope and Method

This review treats the project as hostile-to-itself. Every module, constant, test
vector, document, and artifact is independently checked. The method is:

1. **Re-run every gate** (`audit_c_standard`, `audit_python_standard`,
   `run_tests`, `check_coverage`) and record the exact output and exit codes.
2. **Re-verify every constant** against the generated artifact header and the
   JSON source of truth, by regenerating the header and diffing it.
3. **Re-verify every cryptographic claim** against a published standard vector,
   and separate vectors that run in the native C suite from vectors that run only
   in the Python suite.
4. **Audit the bot adversarially**, with a dedicated Command and Control and
   Botnet section: what it does, how it registers and takes orders, how it stays
   underneath the authenticated path, how it is detected and removed, how it is
   bounded, and what it does not prove.
5. **Re-verify every artifact** by SHA-256 and by the in-repo build guardrail,
   because this project ships no CTF firmware artifact.
6. **Read the documents adversarially** for overclaims, stale numbers, and
   omissions, then correct them in this review.

## 2. Gate Results (all re-run for this review)

| gate | command | observed result |
|---|---|---|
| C standard | `python3 scripts/audit_c_standard.py` | exit 0, no output, **0 violations** |
| Python standard | `python3 scripts/audit_python_standard.py` | exit 0, no output, **0 violations** |
| Native tests | `python3 scripts/run_tests.py` | **479 checks, 0 failures**, 146 test cases |
| Coverage | `python3 scripts/check_coverage.py` | exit 0, **100.00% line coverage**, 2114 owned lines |
| Python suites | `python3 -m unittest test.test_field_crypto test.test_andon_node` | **17 tests, OK** |
| Header guardrail | `python3 scripts/gen_packet.py --from-json scripts/packet_artifact.json --header-out /tmp/ic_psa.h --check-header-path include/packet_artifact.h` | exit 0, `Verified header matches` |

The coverage gate passes on line coverage. It does not require 100% branch or
region coverage, and the raw report is not 100% there: regions 99.27% and
branches 93.69%. That gap is real and is stated in the module table below.

## 3. Module-by-Module Audit

Owned lines are the instrumented statement lines reported by `llvm-cov report`
through `check_coverage.py`. Raw `wc -l` over `src/*.c` includes comments and
blank lines; `main.c` is excluded from coverage by design. Every owned module is
at 100.00% line coverage.

| module | role | owned lines | line coverage | verification performed | honest limitation |
|---|---|---|---|---|---|
| `crc.c` | CRC-16/CCITT-FALSE diagnostic | 20 | 100.00% | `test_crc16_ccitt` check value `0x29B1` | not on the wire; a checksum is not authentication |
| `sensor.c` | DHT11 line-temperature-band classifier | 151 | 100.00% | waveform, all timeout shapes, CRC error, line ok/reject/invalid, negative temperature | mock GPIO replays a recorded waveform, not real silicon |
| `display.c` | HD44780 over PCF8574 | 73 | 100.00% | `test_display_format_lines`, `test_display_render_lines` via recorded I2C | mock I2C, not real HD44780 bus timing |
| `radio.c` | RYLR998 provisioning, `AT+SEND`, `+RCV` parser | 187 | 100.00% | build/parse/reject/pump, oversize guards, spoofed sender attribution | mock UART; the RF band is not simulated |
| `status_led.c` | red/yellow/green FAULT/ACK PENDING/LINE OK tower light | 14 | 100.00% | `test_status_led_show` | none |
| `button.c` | fault-clear/debounce input | 35 | 100.00% | pressed/consume/debounce/reset | active-low input is exercised only through mocks |
| `servo.c` | 50 Hz diverter PWM | 26 | 100.00% | `test_servo_map`, `test_servo_init`, `test_servo_actuate` | mock PWM; no real servo or inrush load |
| `ir_remote.c` | VS1838B NEC fault-clear decode | 116 | 100.00% | decode valid/reject/bad leader/mark/ambiguous/address/command, `test_ir_poll_*` | optical path is unauthenticated; no anti-replay |
| `diverter.c` | line diverter state machine and fail-safe divert policy | 41 | 100.00% | `test_diverter_init`, deploy/retract travel, `test_diverter_fail_safe`, `test_diverter_reject_unauthorized` | bounded travel only; no real gate or load |
| `control.c` | sealed fault command path | 96 | 100.00% | `test_control_handle_success`, bad command, bad zone, bad tag, replay, short body, key guards | shared lab key; guarded set is three commands |
| `andon_auth.c` | anti-replay window and state tag | 74 | 100.00% | `test_andon_auth_state_tag`, apply window/advance/bad tag, null, key, and tag guards | deterministic nonce from sequence; single key |
| `chacha20.c` | ChaCha20 and HChaCha20 | 99 | 100.00% | RFC 8439 block and stream vectors, HChaCha20 draft vector | none |
| `poly1305.c` | Poly1305 one-time authenticator | 169 | 100.00% | RFC 8439 tag vector, aligned path | none |
| `crypto_aead.c` | XChaCha20-Poly1305 seal/open | 38 | 100.00% | round-trip, tamper tag/ct/ad, constant-time `tag_equal` | built from the in-repo primitives, not an audited library |
| `blake2b.c` | BLAKE2b and Argon2 H' | 161 | 100.00% | `test_blake2b_abc`, multiblock, H' 32 and 256 vectors | none |
| `argon2.c` | Argon2id core (BLAMKA, hybrid addressing) | 336 | 100.00% | `test_argon2_lanes`, `test_argon2_type_i`, `test_argon2_clamp` branch coverage | the RFC 9106 KAT runs in Python, not in this C suite |
| `crypto_kdf.c` | Argon2id field key derivation | 28 | 100.00% | reject, empty password, determinism, salt sensitivity | classroom profile `t=3 p=1 m=64`; committed passphrase and salt |
| `envelope.c` | hex nonce/ciphertext/tag codec | 91 | 100.00% | nonce, round-trip, seal/open rejects, uppercase, known vector | none |
| `monitor.c` | andon station controller state machine | 235 | 100.00% | init, idle, render (including infection), temperature, fault-clear remote, remote fault/clear/ack/replay/bad tag/bad command/bad zone, clear no-bypass, link loss, link unseen/within, guards, implant frame delivery | mocks are not the real silicon |
| `implant.c` | SANDBOX_ONLY FROSTLINE command-and-control bot | 124 | 100.00% | first run, re-install on boot, bot marker, debug attached, `C2V1` match, check-in and tasking gates, check-in guard and debug, task blink/log/report/unknown, handle command, tick armed/unarmed, anti-debug, neutralize, clean tick | benign educational bot; build-guarded and breadboard-bound |
| `main.c` | entry point | n/a | excluded | build only | excluded from coverage by design |

**Total owned lines at 100.00% line coverage: 2114.**

Branch coverage below 100% in the same report: `monitor.c` 84.52%, `display.c`
85.71%, `radio.c` 89.87%, `control.c` 90.48%, `andon_auth.c` 92.31%,
`envelope.c` 92.86%, `sensor.c` 94.74%, `ir_remote.c` 96.00%, `argon2.c` 97.56%.
`implant.c` branches are 100.00%.

## 4. Cryptographic Claim Verification

The native suite asserts the following published vectors. Each name below appears
as a passing case in the `run_tests.py` output for this review.

| claim | standard | vector | observed |
|---|---|---|---|
| ChaCha20 block function | RFC 8439 section 2.3.2 | key 00..1f, nonce 000000090000004a00000000 | `test_chacha20_block` PASS |
| ChaCha20 stream cipher | RFC 8439 section 2.4.2 | "Ladies and Gentlemen..." 114-byte ciphertext | `test_chacha20_stream` PASS |
| HChaCha20 subkey | XChaCha20 draft (irtf-cfrg-xchacha) | published subkey vector | `test_hchacha20` PASS |
| Poly1305 tag | RFC 8439 section 2.5.2 | "Cryptographic Forum Research Group" tag `a8061dc1305136c6c22b8baf0c0127a9` | `test_poly1305`, `test_poly1305_aligned` PASS |
| BLAKE2b-512 | BLAKE2 reference | digest of "abc", multiblock, long-input | `test_blake2b_abc`, `test_blake2b_multiblock` PASS |
| Argon2 variable-length hash H' | RFC 9106 section 3.3 | H' of {1,2,3,4} at 32 and 256 bytes | `test_blake2b_long_short`, `test_blake2b_long` PASS |
| Argon2id known-answer | RFC 9106 section 5.3 | `0d640df58d78766c08c037a34a8b53c9d01ef0452d75b65eb52520e96b01e659` | `test.test_field_crypto.TestFieldCrypto.test_rfc9106_argon2id_vector` PASS (Python suite) |
| Envelope layout | project vector | known nonce, node id 7, fixed body | `test_envelope_known_vector` PASS |
| Firmware and Python interop | project vector | shared field key and envelope | `test_field_key_matches_firmware`, `test_envelope_matches_firmware` PASS (Python suite) |

The RFC 9106 Argon2id known-answer test is a Python `unittest` in
`test/test_field_crypto.py`; it is not part of the 479 native checks. Running the
Python suites directly confirms all 17 tests pass, including the KAT and the
firmware-interop vectors.

### 4.1 Sealed fault path, guarded set, and bounded zone band

`src/control.c` opens the envelope under the field key with the andon node id as
associated data, then `control_parse` rejects the body unless the command byte is
one of `ANDON_COMMAND_FAULT` (`0x01`), `ANDON_COMMAND_CLEAR` (`0x02`), or
`ANDON_COMMAND_ACK` (`0x03`), and the decoded zone lies between `ANDON_ZONE_MIN`
(`0`) and `ANDON_ZONE_MAX` (`16`). The guarded set is therefore exactly those
three commands, and the accepted zone band is exactly 0 to 16. The behavior is
asserted by `test_control_handle_success`, `test_control_command_set`,
`test_control_authorize`, `test_control_replay`, `test_control_bad_command`,
`test_control_bad_zone`, `test_control_bad_tag`, `test_control_short_body`, and
`test_control_key_guards`. All pass in this review.

Note that `scripts/gateway.py` sends `ANDON_COMMAND_FAULT = 1`, which agrees with
the firmware decoding `0x01` as fault. The Act V tooling/firmware command-
constant mismatch does not reproduce in this act.

### 4.2 Anti-replay sequence window

`andon_auth_apply` in `src/andon_auth.c` accepts a command only when
`seq > auth->last_seq`, then verifies the keyed tag against the candidate record,
then advances the floor. The behavior is asserted by
`test_andon_auth_apply_window` (accept once, reject the same sequence, reject an
older sequence), `test_andon_auth_apply_advance` (a newer sequence advances
`last_seq`), `test_andon_auth_bad_tag`, and the end-to-end
`test_monitor_remote_replay`. All pass in this review.

Honest limitation: `last_seq` is plain SRAM and resets to zero on every boot, so
a command captured before a reboot can be replayed after one. The paper's Threat
Model states this; the README does not. A production controller would persist the
floor in non-volatile memory.

### 4.3 Authenticated state tag

`andon_auth_state_tag` seals a nine-byte record (`granted`, `seq[4]`,
`last_seq[4]`) under the field key with a nonce built from the sequence and the
domain byte `0xA7`; `andon_auth_state_ok` recomputes and compares in constant
time (`crypto_aead_tag_equal` is a branchless XOR accumulator).
`test_andon_auth_state_tag` proves a modified record fails, and the monitor
paths prove the end-to-end denial before the diverter moves.

Honest limitations: the lab derives the field key and the state-tag key from one
committed secret, so a compromised device can compute tags the gateway accepts;
the tag protects against casual tamper and a debugger that flips `granted`, not a
physical attacker who can read the key out of SRAM and recompute the tag; there
is no per-device key or rotation; and the nonce is deterministic in the sequence,
so two distinct records that ever share a sequence would violate AEAD nonce
uniqueness.

## 5. Command and Control and Botnet Audit

Act VII is the command-and-control act, so the bot gets its own dedicated audit.
The review asks six questions: what it does, how it registers and takes orders,
how it stays underneath the authenticated path, how it is detected and removed,
how it is bounded, and what it does not prove.

### 5.1 What it does

`src/implant.c` is compiled only under `SANDBOX_ONLY`. The clean firmware build
does not define the guard, so the shipping image has no bot. In the
`SANDBOX_ONLY` build:

- **Bot marker with the real flash API.** `implant_init` reads the marker byte
  at `ANDON_IMPLANT_RESERVE_ADDR` (`0x103FF000`). On the first run the marker is
  absent, so `implant_infect` erases the sector and programs `0xC7`
  (`ANDON_IMPLANT_MARKER_BYTE`) with the Pico SDK flash API exactly once. This is
  a real sector erase and program: in the non-mock build the `IMPLANT_FLASH_WRITE`
  macro expands to `flash_range_erase(ANDON_IMPLANT_RESERVE_OFFSET,
  FLASH_SECTOR_SIZE)` followed by `flash_range_program(ANDON_IMPLANT_RESERVE_OFFSET,
  page, FLASH_PAGE_SIZE)` against the final 4 KiB sector of the 4 MiB flash, not a
  simulated memory-mapped store. `implant_infected` reports the marker by
  comparing the reserved byte to `0xC7`.
- **Re-install on boot.** On every later boot the marker is present, so
  `implant_init` sets the armed latch and the payload handler is live again
  without any firmware change. A reflash of the program region does not touch the
  reserved sector.
- **The check-in.** `implant_check_in` builds a six-byte frame of the 4-byte
  magic `C2V1` (`ANDON_IMPLANT_C2_MAGIC`), the bot identifier `0xB7`
  (`ANDON_IMPLANT_BOT_ID`), and a zero task byte, and sends it with
  `radio_send_frame` to the provisioned gateway address. The listener now knows a
  bot is present.
- **Autonomous check-in.** `implant_tick` advances a monotonic counter and, while
  the bot is armed, check-in is enabled, and no probe is attached, emits a
  check-in every `ANDON_IMPLANT_TICK_INTERVAL` (4) ticks. The `g_implant_checkin_count`
  and `g_implant_checked_in` state record the behavior.
- **The benign task handler.** `implant_handle_command` calls
  `implant_magic_match`, which is a `memcmp` against the exact 4-byte preamble on
  the inbound payload. On a match, and when tasking is enabled and no probe is
  attached, it arms the payload, writes the marker, records the task in
  `g_implant_last_task`, increments `g_implant_task_count`, and executes it.
  `implant_execute` runs `ANDON_IMPLANT_TASK_BLINK` (`0x01`, a tower light
  blink), `ANDON_IMPLANT_TASK_LOG` (`0x02`, a synthetic log line), or
  `ANDON_IMPLANT_TASK_REPORT` (`0x03`, a status report frame), and ignores an
  unknown task code.
- **Anti-debug.** `implant_debug_attached` reads CoreDebug `DHCSR` at
  `0xE000EDF0` (`ANDON_IMPLANT_DHCSR_ADDR`). Bit 0 is `C_DEBUGEN` and bit 1 is
  `C_HALT`. `implant_ready` and `implant_tasking_frame` return false when either
  bit is set, so the check-in and the task handler are both suppressed while a
  probe is attached.

Every one of these behaviors is asserted by a native test:
`test_monitor_implant_frame`, `test_implant_init_first_run`,
`test_implant_reinstall_on_boot`, `test_implant_marker`,
`test_implant_debug_attached`, `test_implant_checkin_guard`,
`test_implant_checkin_debug`, `test_implant_magic_match`,
`test_implant_tasking_accept`, `test_implant_tasking_gate`,
`test_implant_task_blink`, `test_implant_task_log_report`,
`test_implant_task_unknown`, `test_implant_tick`, `test_implant_tick_unarmed`,
`test_implant_anti_debug`, and `test_implant_neutralize`. The Python adapter
additionally asserts the tasking with `test_09_implant_tasking_accept`. All pass
in this review.

### 5.2 How the check-in and tasking work, and why a quiet radio is not a clean radio

The command path is deliberately narrow and deliberately quiet. An armed,
unobserved node emits a six-byte check-in frame every four ticks, and it runs a
benign task when a `C2V1` tasking frame arrives on the raw payload. Nothing in
the path opens an envelope, presents a key, or changes a byte that an AEAD tag
covers, so an integrity check over content is blind to it. The state is the
`0xC7` marker in the reserved sector and the runtime flags in SRAM, and the loop
is the four-tick check-in. A firmware reflash writes the program region and
leaves the marker alone, so the node comes back armed.

### 5.3 How it stays underneath the authenticated path

The bot is not a stealth protocol client. It never builds an envelope, never
holds a key, and never calls `control_handle_frame`. Its entire inbound surface
is `memcmp` against the 4-byte `C2V1` preamble on the raw frame, and its entire
outbound surface is the same radio send path used by ordinary telemetry. That is
the architectural point: the sealed fault path can be correct, tested, and
replay-resistant, and a listener that sits below it is unaffected by every one of
those properties. The monitor's ordering makes the lesson explicit in one
function: `monitor_apply_frame` delivers the raw payload to `monitor_implant_frame`
before it calls the sealed `control_handle_frame`. The bot matches bytes
underneath the authenticated path, which is why a correct sealed command path
does not protect a listener that sits below it.

### 5.4 How it is detected and removed

- **By build comparison.** The clean and `SANDBOX_ONLY` images differ by the
  implant translation unit and its symbols, which is the simplest and strongest
  detection: the payload is absent from the shipping build.
- **By reserved-sector inspection.** The marker at `0x103FF000` is state the
  firmware image does not own, and it is visible with the Debug Probe or
  `picotool`. Because it is written with the real flash API, a read of the sector
  returns the `0xC7` byte on physical silicon.
- **By link signature.** The six-byte `C2V1` check-in frame, its `0xB7` bot
  identifier, and its four-tick cadence are a static and dynamic signature.
- **By display mismatch.** The `I:INF` infection field on the LCD next to a quiet
  control link is the bot's fingerprint.
- **By static analysis.** The `C2V1` magic, the `0xB7` bot id, the task codes,
  the `DHCSR` read address, and the reserved-sector address are all literal
  constants in the image.
- **By controlled observation.** Because the anti-debug branch is a single early
  return, a student can break after it under GDB and observe the check-in and the
  task handler resume, which proves the payload rather than merely suspecting it.
- **By removal.** The documented fix is not one step: close the check-in gate and
  the tasking gate (`implant_set_checkin(false)`,
  `implant_set_tasking(false)`), clear the runtime state and the marker
  (`implant_neutralize`), erase the reserved sector, and remove the re-install
  check and the `SANDBOX_ONLY` build flag so no future boot trusts the marker.

### 5.5 How it is bounded

The bot is bounded by construction and by test. It touches only its own radio
frames, its runtime flags, and the one reserved sector. It has no network, no
filesystem, and no host impact. Its tasks are benign. It never actuates the
diverter and never opens the sealed path. The `SANDBOX_ONLY` guard is the
containment boundary, the reserved sector is on the same chip and holds nothing
else, and the native bot tests assert both the behavior and its limits.

### 5.6 What it does not prove, and the honest limitation

The bot is a **benign educational command-and-control implant**. It is confined
to the breadboard, guarded by `SANDBOX_ONLY`, and has no network. It emits only
to the student's own LoRa modules on the classroom network id, it actuates only
the tower light blink task, and it writes only to a reserved sector on the same
chip that holds nothing else. **The C2 is a local classroom script.** There is no
external address, no internet path, no remote server, and no command-and-control
endpoint on a network. The listener is a local script you run yourself, over your
own radio, on your own network id. **The tasks are benign**: a tower light blink,
a synthetic log line, and a status report, with no destructive, exfiltrating, or
network-capable action. **No external network exists** anywhere in this project.
It is a demonstration of technique, not tradecraft: it does not load a second
stage, it does not encrypt its tasking, it does not persist across a deliberate
sector erase, and it does not resist physical forensics. Any claim that this
module is operationally representative of a real command-and-control implant
would be an overclaim, and this review records that plainly.

The deeper honest limitation is architectural and does not go away with a cleaner
implementation: the bot bypasses the sealed fault path by listening to the raw
payload before it is authenticated, and it registers with a listener the factory
never named. No amount of wire authentication fixes the first, and no
authorization check on a different path fixes the second. Mitigating them is a
build-integrity, image-signing, authorization-boundary, and debug-lockdown
control, which is why the blue half names those controls rather than pretending
the protocol covers them.

## 6. Artifact Verification

This project ships no CTF firmware artifact (`build/` holds only untracked local
test binaries). The companion CTF is external:
`https://github.com/mytechnotalent/CTF_factory-andon-station`, which ships the
compromised image with the bot and its verifier. What is verified in this
repository is the source tree and the provisioning artifact.

Source-tree aggregate SHA-256 over all 44 `.c` and `.h` files under `src/` and
`include/`, computed as `find src include \( -name '*.c' -o -name '*.h' \) |
sort | xargs shasum -a 256 | shasum -a 256`:

```
314bedd5fef595f6fe4dead2ea942be233c9f620404e710f8da8744d2e2f0898
```

Key artifacts by SHA-256:

```
paper.pdf                     4e38ed884016e5e08c6964ac1e57ae12ba5807899ae7c065537e0b11eb9b3303
paper.typ                     c2e351cd1fe05994a2612b870b8779fb80401e191c769ac652d076265496aba4
factory-andon-station.png     3c71aa43074693fe7b88a40bd1acf0639fe324c19ee76f6db47c76a1b6073d7e
scripts/packet_artifact.json  f1f42d53b4383865e33600bad70d9b7d8d2357cfb4a82be46a7a9cd2cf10d6de
include/packet_artifact.h     1806f1e9fdbd28b8fe863e104d1dea5c6f3663678e27a78693243f8999c8f860
include/field_secrets.h       63cffbbeb9e4740c4031865d6bcf5bb8302ede090579eb4fbf0ef41764135d07
```

The build guardrail `check_packet_artifact_header` regenerates
`include/packet_artifact.h` from `scripts/packet_artifact.json` and fails if the
committed header is stale. Re-run for this review:

```
$ python3 scripts/gen_packet.py --from-json scripts/packet_artifact.json \
      --header-out /tmp/ic_psa.h --check-header-path include/packet_artifact.h
Wrote generated firmware header: /tmp/ic_psa.h
Verified header matches: .../include/packet_artifact.h
exit=0
```

Constants re-read from `include/packet_artifact.h` and matched to the README and
the pin map: `PACKET_NODE_ADDRESS` 7, `PACKET_GATEWAY_ADDRESS` 0x0001,
`PACKET_FRAME_SIZE` 48, `PACKET_LINK_WAIT_MS` 5000,
`PACKET_DIVERTER_RETRACT_PULSE_US` 500, `PACKET_DIVERTER_DEPLOY_PULSE_US` 1500,
`PACKET_DHT_TIMEOUT_US` 240, `PACKET_LCD_I2C_ADDRESS` 0x27,
`PACKET_MAX_RCV_LEN` 256, plus the shared pin map.

The banner is generated by `scripts/gen_banner.py` and is 1500 x 1500 pixels,
matching the other acts.

## 7. Adversarial Document Review

| document claim | audit verdict |
|---|---|
| README does not imply the device is unhackable | **accurate**; it states the fault path is sealed, that the bot hides underneath it, and that removal is not a single patch |
| README states the bot is benign, guarded, and confined | **accurate**; it appears in the narrative, the bot section, Lab 3, PARTS.md, and this review |
| README states the tasks are benign and locally scoped | **accurate**; it is stated in the narrative, the FROSTLINE Command-and-Control Bot section, Lab 3, PARTS.md, and this review |
| README gives the marker, magic, bot id, tasks, interval, and anti-debug details | **accurate**; the `0xC7` marker, `C2V1` magic, `0xB7` bot id, the three task codes, the six-byte frame, the 4-tick interval, the `DHCSR` bits, and the reserved address all match the firmware |
| README states the clean build does not define `SANDBOX_ONLY` | **accurate**; the CMake option defaults to OFF and the module is guarded |
| README "The suite has **146 cases** and **479 checks**" | **accurate**; the native runner reports exactly 146 cases and 479 checks |
| README claims 100% line coverage of owned modules | **accurate**; the report is 2114 / 2114 lines |
| README claims the bot marker uses the real flash API | **accurate**; the non-mock `implant_flash_write` calls `flash_range_erase` and `flash_range_program` |
| README does not mention per-device key rotation | **omission**; the paper's Threat Model is the only place that states the single-key limitation |
| README anti-replay section does not mention the reboot reset | **omission**; the paper states it, the README does not |
| README states the C2 is a local script with no external network | **accurate**; the bot section, Lab 3, PARTS.md, and this review all state it |
| Gateway/firmware command constant | **accurate**; `gateway.py` `ANDON_COMMAND_FAULT = 1` matches the firmware `0x01` fault code, unlike Act V |

Corrections: the README's anti-replay and key-model sections should carry the
same reboot-reset and no-per-device-rotation caveats the paper already carries.
No claim of unhackability was found, and the Act V tooling command-constant
mismatch does not reproduce.

## 8. Honest Limitations

- **Physical access wins.** A Debug Probe over SWD can read the field key from
  SRAM. The authenticated state tag detects a flipped verdict, but a probe that
  can read the key and recompute the tag defeats the design. Only OTP debug
  disable closes this.
- **Key extraction from flash.** `include/field_secrets.h` commits the passphrase
  and salt. Anyone holding the image holds the key. This is a lab convenience,
  not a deployment.
- **Single shared field key.** Both the wire key and the state-tag key derive
  from one committed secret, so a compromised device can compute tags the gateway
  accepts. There is no per-device key and no rotation in this build.
- **Replay after reboot.** `last_seq` resets to zero, so a command captured
  before a power cycle can be replayed after it. The floor is not persisted.
- **Classroom crypto profile.** Argon2id runs at `t=3 p=1 m=64` to fit SRAM; the
  state-tag nonce is deterministic in the sequence; both are teaching parameters,
  not hardening parameters.
- **The bot takes only benign tasks.** The tasks are a tower light blink, a log
  line, and a status report. This is a hard scope limit, not an implementation
  detail.
- **The C2 is a local script.** The listener is a local classroom script over the
  student's own radio on the student's own network id. There is no external
  address, no internet path, and no remote command-and-control endpoint. No
  external network exists.
- **The bot is inert, guarded, and breadth-limited.** It is benign,
  breadboard-bound, `SANDBOX_ONLY`-guarded, networkless, and confined to a
  reserved sector on the same chip. Its persistence is persistence against a
  firmware reflash, not against a deliberate sector erase or physical forensics.
  It demonstrates technique, not tradecraft.
- **The bot bypasses the protocol.** A module that reads the raw payload and
  answers its own listener is a build-integrity, authorization-boundary, and
  debug-lockdown problem, not a wire-authentication problem. Signing, gating, and
  debug lockdown are named as the real controls.
- **Anti-debug detectability is not taught to deployment depth.** The `DHCSR`
  check is deliberately simple; hardening against a determined analyst is out of
  scope.
- **Unauthenticated optical input.** Any NEC remote can send a fault clear. The
  optical surface is a documented exposure; the sealed radio path is the
  authorization path.
- **Fault-clear request is a single input.** It is debounced and it never
  bypasses authorization, but it is one button; a failed button or a stuck line
  is a hardware reliability problem outside the firmware's control.
- **Supply chain and sensor trust are out of scope.** The DHT11 is checksummed,
  not authenticated, and the firmware is only as trustworthy as the toolchain and
  the parts.
- **Availability is not protected.** An attacker on the band can jam or flood the
  receiver.
- **Coverage is line coverage.** Branch coverage is not 100%, and the harness
  mocks are not the real silicon.

## 9. Conclusion

The project is internally consistent and candid: 20 owned modules, 2114
instrumented lines, 100.00% line coverage, 479 native checks passing with 0
failures, 146 native cases, and 17 passing Python tests, every cryptographic
primitive anchored to a published vector. The six gates all pass with exit 0.
Act VII adds real command-and-control behavior over Acts I to VI: a six-byte
`C2V1` check-in carrying the `0xB7` bot identifier, a benign remote task handler,
a reserved-sector bot marker written with the real Pico SDK flash API that
survives a firmware reflash, a re-install on every boot, and a four-tick
check-in loop that announces the bot whether or not anyone is listening. It keeps
the sealed and guarded fault command path, the strictly monotonic anti-replay
window, and the keyed tag over the authorization record, all exercised end to
end, and it adds a fault-clear request that asks for authorization instead of
bypassing it. Every bot behavior is asserted by a native test and every limit is
stated. The documentation is unusually honest about the shared key, the open
debug port, the inert bot, the benign tasks, the local script listener, and the
scope limit that it targets only the local classroom listener, with minor
omissions (reboot reset and no per-device rotation) that should be folded into
the README. The core lesson holds and is stated: the wire is sealed, the verdict
is tagged, the clear request cannot bypass, and the remaining risk is the key,
the probe, the local listener, and the silence of a station that is taking
orders.

---

*This review is reproducible: run the six commands in section 2, the header
check in section 6, and the Python suites in section 4.*

This is Act VII of the ten-act OPERATION COLD IRON saga. See SAGA.md.
