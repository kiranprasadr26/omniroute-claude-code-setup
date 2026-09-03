# OmniRoute + Claude Code — Windows Setup

A simple guide to configure **OmniRoute as a local AI proxy for Claude Code on Windows**.

This setup allows Claude Code to connect to OmniRoute through a local endpoint:

```text
http://localhost:20128/v1
```

OmniRoute can then route requests to the AI providers/models configured in OmniRoute.

---

## 📌 Architecture

```text
                         ┌──────────────────────┐
                         │     Claude Code      │
                         │                      │
                         │  claude              │
                         └──────────┬───────────┘
                                    │
                                    │ HTTP
                                    ▼
                         ┌──────────────────────┐
                         │      OmniRoute       │
                         │                      │
                         │ localhost:20128      │
                         └──────────┬───────────┘
                                    │
                  ┌─────────────────┼─────────────────┐
                  │                 │                 │
                  ▼                 ▼                 ▼
             Provider 1        Provider 2        Provider 3
                  │                 │                 │
                  ▼                 ▼                 ▼
                Model             Model             Model
```

---

# 🚀 Prerequisites

Before starting, make sure you have:

* Windows 10 or Windows 11
* Node.js LTS
* npm
* Git
* Claude Code
* OmniRoute

---

# 1. Check Node.js and npm

Open **Command Prompt (CMD)**.

Run:

```cmd
node -v
```

Then:

```cmd
npm -v
```

Example:

```text
C:\> node -v
v22.x.x

C:\> npm -v
10.x.x
```

If both commands return a version number, Node.js and npm are installed correctly.

---

# 2. Install Claude Code

Install Claude Code globally:

```cmd
npm install -g @anthropic-ai/claude-code
```

Verify:

```cmd
claude --version
```

If the version is displayed, Claude Code is installed.

---

# 3. Install OmniRoute

Install OmniRoute globally:

```cmd
npm install -g omniroute
```

Verify:

```cmd
omniroute --help
```

If the help information is displayed, OmniRoute is installed correctly.

---

# 4. Start OmniRoute

Open **Command Prompt 1**.

Run:

```cmd
omniroute
```

Depending on the installed version, you may also need:

```cmd
omniroute serve
```

Keep this terminal open.

OmniRoute should provide a local service on:

```text
http://localhost:20128
```

The API endpoint used by Claude Code is:

```text
http://localhost:20128/v1
```

---

# 5. Open the OmniRoute Dashboard

Open your browser and go to:

```text
http://localhost:20128
```

From the OmniRoute dashboard, configure the AI provider(s) you want to use.

After configuring the provider, create an OmniRoute API key.

Keep the API key private.

---

# 6. Create Your Claude Code Project

Open **Command Prompt 2**.

Navigate to your project:

```cmd
cd C:\Users\Lenovo\workspace\test
```

If the directory does not exist:

```cmd
mkdir C:\Users\Lenovo\workspace\test
```

Then:

```cmd
cd C:\Users\Lenovo\workspace\test
```

---

# 7. Create the `.claude` Directory

Run:

```cmd
mkdir .claude
```

Your project should now look like:

```text
C:\Users\Lenovo\workspace\test
│
└── .claude
```

---

# 8. Create Claude Code Configuration

Create:

```text
.claude\settings.local.json
```

You can use Notepad:

```cmd
notepad .claude\settings.local.json
```

If Windows asks whether you want to create the file, click **Yes**.

Add:

```json
{
  "anthropic_api_url": "http://localhost:20128/v1",
  "anthropic_api_key": "YOUR_OMNIROUTE_API_KEY",
  "model": "auto"
}
```

Replace:

```text
YOUR_OMNIROUTE_API_KEY
```

with your actual OmniRoute API key.

For example:

```json
{
  "anthropic_api_url": "http://localhost:20128/v1",
  "anthropic_api_key": "YOUR_REAL_KEY_HERE",
  "model": "auto"
}
```

Save the file with:

```text
Ctrl + S
```

Then close Notepad.

---

# 🔐 IMPORTANT — API Key Security

**Never commit your real API key to GitHub.**

Do NOT put a real key inside:

```text
settings.local.json
```

if that file is going to be committed.

Instead, keep:

```text
settings.local.json
```

local to your computer.

For your GitHub repository, provide an example file:

```text
settings.local.example.json
```

Example:

```json
{
  "anthropic_api_url": "http://localhost:20128/v1",
  "anthropic_api_key": "YOUR_OMNIROUTE_API_KEY",
  "model": "auto"
}
```

---

# 9. Add `.gitignore`

If this setup is also stored in GitHub, create a `.gitignore` file.

Example:

```gitignore
# Claude Code local configuration
.claude/settings.local.json

# Environment files
.env
.env.*

# Node.js
node_modules/

# Logs
*.log

# Windows
Thumbs.db
Desktop.ini

# macOS
.DS_Store
```

This prevents your local credentials from accidentally being committed.

---

# 10. Clear Conflicting Environment Variables

Claude Code may use environment variables instead of the local configuration.

Inside your project CMD window, run:

```cmd
set ANTHROPIC_API_KEY=
```

This clears the API key for the current CMD session.

You can verify it with:

```cmd
echo %ANTHROPIC_API_KEY%
```

It should return an empty line.

> If you have configured `ANTHROPIC_API_KEY` as a permanent Windows environment variable, remove or update it separately if it conflicts with your OmniRoute setup.

---

# 11. Start Claude Code

Make sure you are inside your project:

```cmd
cd C:\Users\Lenovo\workspace\test
```

Then:

```cmd
claude
```

Claude Code should now use OmniRoute through:

```text
http://localhost:20128/v1
```

---

# 🔄 Daily Startup After Windows Restart

You do **not** need to reinstall anything every time.

You only need to start OmniRoute and Claude Code.

## Terminal 1 — OmniRoute

Open CMD:

```cmd
omniroute
```

Keep this terminal running.

You can minimize it.

---

## Terminal 2 — Claude Code

Open another CMD:

```cmd
cd C:\Users\Lenovo\workspace\test
```

Clear any conflicting API key:

```cmd
set ANTHROPIC_API_KEY=
```

Start Claude:

```cmd
claude
```

That's it.

---

# ⚡ Quick Start

After everything has been installed, your normal workflow is:

### CMD 1

```cmd
omniroute
```

### CMD 2

```cmd
cd C:\Users\Lenovo\workspace\test
set ANTHROPIC_API_KEY=
claude
```

---

# 🧪 Troubleshooting

## OmniRoute command not found

If you get:

```text
'omniroute' is not recognized as an internal or external command
```

Check:

```cmd
npm -g bin
```

Then check the npm global installation:

```cmd
npm list -g --depth=0
```

You should see:

```text
omniroute
```

You can also reinstall:

```cmd
npm install -g omniroute
```

---

# 🔍 Check OmniRoute Port

To check whether OmniRoute is listening on port `20128`:

```cmd
netstat -ano | findstr :20128
```

If it is running, you should see something similar to:

```text
TCP    127.0.0.1:20128    0.0.0.0:0    LISTENING
```

---

# 🌐 Test OmniRoute Dashboard

Open:

```text
http://localhost:20128
```

If the dashboard loads, OmniRoute is running.

---

# 🔎 Check Claude Code

Check the Claude Code installation:

```cmd
claude --version
```

Check the available commands:

```cmd
claude --help
```

---

# 🛑 OmniRoute Is Not Running

If Claude Code cannot connect to OmniRoute, first check:

```cmd
netstat -ano | findstr :20128
```

If nothing is returned, start OmniRoute:

```cmd
omniroute
```

Then retry:

```cmd
claude
```

---

# 🔐 API Key Problems

If OmniRoute reports an authentication error:

1. Open the OmniRoute dashboard.
2. Verify the provider configuration.
3. Generate/check your OmniRoute API key.
4. Update:

```text
.claude\settings.local.json
```

5. Restart Claude Code.

---

# 🤖 Model Configuration

This setup uses:

```json
"model": "auto"
```

The `auto` model allows OmniRoute to handle model routing based on its configured providers.

If your OmniRoute version/documentation specifies a different model identifier, use the identifier supported by that version.

---

# 📁 Recommended Repository Structure

A clean GitHub repository can look like this:

```text
omniroute-claude-code-setup/
│
├── README.md
│
├── windows/
│   ├── install.cmd
│   ├── start-omniroute.cmd
│   └── start-claude.cmd
│
├── claude/
│   └── settings.local.example.json
│
└── .gitignore
```

---

# 🛠️ Optional Windows Scripts

## `windows/install.cmd`

```bat
@echo off

echo ==========================================
echo OmniRoute + Claude Code Installation
echo ==========================================

echo.
echo Checking Node.js...
node -v

echo.
echo Checking npm...
npm -v

echo.
echo Installing OmniRoute...
npm install -g omniroute

echo.
echo Installing Claude Code...
npm install -g @anthropic-ai/claude-code

echo.
echo Installation completed.

pause
```

---

## `windows/start-omniroute.cmd`

```bat
@echo off

echo ==========================================
echo Starting OmniRoute
echo ==========================================

omniroute

pause
```

---

## `windows/start-claude.cmd`

Update the project path if required:

```bat
@echo off

echo ==========================================
echo Starting Claude Code
echo ==========================================

cd /d C:\Users\Lenovo\workspace\test

set ANTHROPIC_API_KEY=

claude

pause
```

---

# 📋 Configuration Summary

| Component           | Configuration                    |
| ------------------- | -------------------------------- |
| Operating System    | Windows 10/11                    |
| Node.js             | LTS                              |
| Claude Code         | npm global installation          |
| OmniRoute           | npm global installation          |
| OmniRoute Dashboard | `http://localhost:20128`         |
| OmniRoute API       | `http://localhost:20128/v1`      |
| Claude Model        | `auto`                           |
| Project             | `C:\Users\Lenovo\workspace\test` |
| Claude Config       | `.claude/settings.local.json`    |

---

# 🔗 Official OmniRoute

Official GitHub repository:

https://github.com/diegosouzapw/OmniRoute

Before following commands from this guide, check the official OmniRoute documentation for changes to installation commands, configuration options, ports, or supported model names.

---

# ⚠️ Security Checklist

Before pushing this repository to GitHub:

* [ ] No real API keys in `README.md`
* [ ] No real API keys in `.cmd` files
* [ ] No real API keys in `.json` example files
* [ ] `.claude/settings.local.json` is in `.gitignore`
* [ ] `.env` is in `.gitignore`
* [ ] Provider credentials are not committed
* [ ] Previously exposed keys have been revoked/rotated

---

# 📝 License

This repository contains setup documentation and helper scripts for configuring OmniRoute and Claude Code.

OmniRoute and Claude Code are separate projects with their own licenses and terms.
