---
name: ung220z-tool
description: Operate the UNG220Z/Unionman ONT credential-recovery toolkit and its multi-vendor ontkit commands on devices the user owns or is explicitly authorized to service. Use for ONT firmware fingerprinting, forgotten admin credentials, PPPoE/WiFi migration, and Telnet/configuration self-checks.
metadata:
  short-description: Recover and audit credentials on authorized ONT/optical-modem devices
---

# UNG220Z / ONT Toolkit

Use this skill only against an ONT/optical modem that the user owns or is explicitly authorized to service. Never use it against an ISP, customer, employer, or third-party device without written authorization.

## Safety and approval gates

- Start with the least invasive command: `python3 -m ontkit probe -H <device-ip>`.
- Do not accept a public IP or an address outside the user's local network unless the user confirms the exact authorized device and network path.
- Prefer the script's interactive password prompt. Do not put passwords on the command line, in a shell history, or in chat.
- Ask for explicit confirmation immediately before any action that changes the device:
  - `--enable-telnet`
  - `--unlock`
  - factory-mode commands that write Telnet settings
  - any reboot, service restart, or configuration save
- Avoid repeated failed logins. Most devices lock for 10–30 minutes after about three failures.
- Treat any extracted credential or configuration log as sensitive. Keep it local, use mode `0600`, and never upload or paste it into chat.

## Workflow

1. Identify the device family without changing it:

```bash
cd "${CODEX_HOME:-$HOME/.codex}/skills/ung220z-tool"
python3 -m ontkit probe -H <device-ip>
```

2. Choose the matching path:
   - UNG220Z / Unionman `.gch`: `python3 ung220z.py -i <device-ip> --no-browser`
   - ZTE family: `python3 -m ontkit zte -H <device-ip> -p 8080 --mode web`
   - Huawei family: `python3 -m ontkit huawei -H <device-ip>`
   - FiberHome family: `python3 -m ontkit fiberhome -H <device-ip>`

3. Run the least destructive mode first. For ZTE commands that can modify Telnet state, obtain approval before adding `--enable-telnet` or changing `--mode`.

4. Interpret failures using the project's exit-code and troubleshooting guidance in `README.md` and `ontkit/README.md`. Do not silently retry authentication.

## Local validation

Before a first real-device run, or after updating the tool, verify that the code imports and the CLI help renders without touching a device:

```bash
cd "${CODEX_HOME:-$HOME/.codex}/skills/ung220z-tool"
python3 -m py_compile ung220z.py ontkit/*.py models/*.py
python3 -m ontkit --help
```

## Project-specific references

- Read `README.md` for the UNG220Z Telnet flow, parameter meanings, exit codes, and supported models.
- Read `ontkit/README.md` for ZTE/Huawei/FiberHome differences and the experimental-status table.
- Only the UNG220Z path is author-verified on a real device; other model support is community-derived and must be described as such.

## Installation provenance

- Upstream: `https://github.com/hut-hzf/ung220z-tool`
- User fork: `https://github.com/holdbreath/ung220z-tool`
