---
name: ssh-command-screenshot
description: Run SSH commands in Windows Terminal and capture per-command screenshot evidence into a new folder. Use when Codex must perform command-line evidence collection, security baseline checks, incident-response verification, or audit screenshots over SSH on Windows without modifying Excel or other report files.
---

# SSH Command Screenshot

## Purpose

Use this skill to collect visual evidence for remote SSH command execution from a Windows host. The workflow opens Windows Terminal, connects to a target with `ssh`, runs a list of commands, captures one PNG screenshot per command, and writes a manifest into a timestamped evidence folder.

Do not use this skill for Excel/report editing. Keep this skill limited to command execution and screenshot evidence collection.

## Workflow

1. Create or identify a JSON command list. Use `scripts/sample-commands.json` as the format reference.
2. Run `scripts/capture-ssh-evidence.ps1` from Windows PowerShell.
3. Verify the output folder contains `rowXX.png`/`cmdXX.png` screenshots and `manifest.json`.
4. Report the output folder and any collection failures.

## Command List Format

Use a JSON array of command objects:

```json
[
  {
    "id": "row05",
    "name": "passwd-shadow-check",
    "command": "clear; echo 'ROW 05'; cat /etc/passwd; awk -F: '{print $1}' /etc/shadow",
    "waitSeconds": 2
  }
]
```

Fields:
- `id`: screenshot filename prefix; use stable ASCII such as `row05` or `cmd01`.
- `name`: short descriptive label for `manifest.json`.
- `command`: shell command pasted into the SSH session.
- `waitSeconds`: optional delay before screenshot; increase for long-running commands.

## Script Usage

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\capture-ssh-evidence.ps1 `
  -HostName 192.168.110.213 `
  -User root `
  -Password '123456' `
  -CommandsJson .\scripts\sample-commands.json `
  -OutputRoot D:\WorkSpace\Evidence
```

The script creates:

```text
<OutputRoot>\<HostName>_<timestamp>\
  manifest.json
  row05.png
  row06.png
  ...
```

## Operational Notes

- Prefer key-based SSH when possible. If `-Password` is supplied, the script pastes it into Windows Terminal for interactive login.
- Keep commands read-only unless the user explicitly authorizes changes.
- Use `clear; echo 'ROW XX ...'; echo 'COMMAND: ...'; <command>; echo 'END ROW XX'` so each screenshot is self-describing.
- Use `-TerminalRows` and `-TerminalCols` to fit long outputs in screenshots; defaults are 60 rows and 180 columns.
- If a command output is too long for one screenshot, split it into multiple command objects.
- The script captures the Windows Terminal window using `PrintWindow`; avoid covering/minimizing the terminal during collection.

## Validation

Before a real collection, validate local prerequisites and command JSON:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\capture-ssh-evidence.ps1 `
  -HostName example.local -User root -CommandsJson .\scripts\sample-commands.json -ValidateOnly
```
