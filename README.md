# SSH Command Screenshot Skill / SSH 命令截图取证 Skill

## English

### Overview

`ssh-command-screenshot` is a Codex skill for running SSH commands in Windows Terminal and capturing screenshot evidence for each command. It is designed for audit evidence collection, security baseline checks, incident-response verification, and other command-line proof workflows.

This skill **does not edit Excel, Word, or report files**. It only performs command execution, screenshots, and evidence export.

### What It Does

- Opens Windows Terminal on the local Windows machine.
- Connects to a remote host through `ssh`.
- Runs commands from a JSON command list.
- Captures one PNG screenshot per command.
- Exports screenshots and `manifest.json` to a timestamped evidence folder.

### Folder Structure

```text
ssh-command-screenshot/
  SKILL.md
  README.md
  agents/
    openai.yaml
  scripts/
    capture-ssh-evidence.ps1
    sample-commands.json
```

### Requirements

- Windows
- Windows Terminal (`wt.exe`)
- OpenSSH client (`ssh.exe`)
- PowerShell
- Network access to the SSH target
- Valid SSH credentials or key-based access

### Quick Start

```powershell
powershell -ExecutionPolicy Bypass -File D:\WorkSpace\Skill\ssh-command-screenshot\scripts\capture-ssh-evidence.ps1 `
  -HostName 192.168.110.213 `
  -User root `
  -Password '123456' `
  -CommandsJson D:\WorkSpace\Skill\ssh-command-screenshot\scripts\sample-commands.json `
  -OutputRoot D:\WorkSpace\Evidence
```

The output folder will look like:

```text
D:\WorkSpace\Evidence\192.168.110.213_YYYYMMDD_HHMMSS\
  01_row05.png
  02_row06.png
  manifest.json
```

### Command JSON Format

Create a JSON file containing command objects:

```json
[
  {
    "id": "row05",
    "name": "passwd-shadow-empty-password-check",
    "command": "clear; echo 'ROW 05'; cat /etc/passwd; echo 'END ROW 05'",
    "waitSeconds": 2
  }
]
```

Fields:

- `id`: Used in the screenshot filename.
- `name`: Human-readable description stored in `manifest.json`.
- `command`: Command pasted into the SSH terminal.
- `waitSeconds`: Seconds to wait before taking the screenshot.

### Validate Only

Use `-ValidateOnly` to check prerequisites and JSON format without opening SSH:

```powershell
powershell -ExecutionPolicy Bypass -File D:\WorkSpace\Skill\ssh-command-screenshot\scripts\capture-ssh-evidence.ps1 `
  -HostName example.local `
  -User root `
  -CommandsJson D:\WorkSpace\Skill\ssh-command-screenshot\scripts\sample-commands.json `
  -ValidateOnly
```

### Notes

- Prefer read-only commands for audit evidence.
- Split long command output into multiple commands so each screenshot remains readable.
- Avoid minimizing or covering Windows Terminal during capture.
- Prefer SSH keys over passwords when possible.
- Do not store real passwords in committed JSON files.

---

## 中文

### 概述

`ssh-command-screenshot` 是一个 Codex Skill，用于在本机 Windows Terminal 中通过 SSH 执行远程命令，并对每条命令的执行结果进行截图取证。适用于安全基线检查、审计留痕、应急响应核查、远程命令执行证明等场景。

该 Skill **不处理 Excel、Word 或报告文件**，只负责命令执行、截图和证据导出。

### 功能

- 在本机 Windows 上打开 Windows Terminal。
- 使用 `ssh` 连接远程主机。
- 按 JSON 命令清单逐条执行命令。
- 每条命令保存一张 PNG 截图。
- 将截图和 `manifest.json` 导出到带时间戳的新证据文件夹。

### 目录结构

```text
ssh-command-screenshot/
  SKILL.md
  README.md
  agents/
    openai.yaml
  scripts/
    capture-ssh-evidence.ps1
    sample-commands.json
```

### 运行要求

- Windows 系统
- Windows Terminal（`wt.exe`）
- OpenSSH 客户端（`ssh.exe`）
- PowerShell
- 能访问目标 SSH 主机
- 有效的 SSH 密码或密钥认证

### 快速使用

```powershell
powershell -ExecutionPolicy Bypass -File D:\WorkSpace\Skill\ssh-command-screenshot\scripts\capture-ssh-evidence.ps1 `
  -HostName 192.168.110.213 `
  -User root `
  -Password '123456' `
  -CommandsJson D:\WorkSpace\Skill\ssh-command-screenshot\scripts\sample-commands.json `
  -OutputRoot D:\WorkSpace\Evidence
```

输出目录示例：

```text
D:\WorkSpace\Evidence\192.168.110.213_YYYYMMDD_HHMMSS\
  01_row05.png
  02_row06.png
  manifest.json
```

### 命令 JSON 格式

创建一个 JSON 文件，内容为命令对象数组：

```json
[
  {
    "id": "row05",
    "name": "passwd-shadow-empty-password-check",
    "command": "clear; echo 'ROW 05'; cat /etc/passwd; echo 'END ROW 05'",
    "waitSeconds": 2
  }
]
```

字段说明：

- `id`：用于截图文件名。
- `name`：命令说明，会写入 `manifest.json`。
- `command`：粘贴到 SSH 终端执行的命令。
- `waitSeconds`：命令执行后等待多少秒再截图。

### 仅校验环境

使用 `-ValidateOnly` 可以只校验依赖和 JSON 格式，不实际连接 SSH：

```powershell
powershell -ExecutionPolicy Bypass -File D:\WorkSpace\Skill\ssh-command-screenshot\scripts\capture-ssh-evidence.ps1 `
  -HostName example.local `
  -User root `
  -CommandsJson D:\WorkSpace\Skill\ssh-command-screenshot\scripts\sample-commands.json `
  -ValidateOnly
```

### 注意事项

- 审计取证场景优先使用只读命令。
- 如果命令输出过长，建议拆分成多条命令，保证截图可读。
- 截图过程中不要最小化或遮挡 Windows Terminal。
- 如条件允许，优先使用 SSH 密钥认证，减少明文密码使用。
- 不要把真实密码写入并提交到 JSON 文件中。
