# OmniRoute + Claude Code — Windows Setup

A complete Windows setup guide for using **OmniRoute as a local AI gateway for Claude Code**.

This setup allows Claude Code to send Anthropic-compatible requests to a local OmniRoute instance. OmniRoute then routes those requests to the providers and models configured in its dashboard.

```text
Claude Code
     │
     │ Anthropic API
     ▼
OmniRoute
localhost:20128
     │
     ├── Anthropic
     ├── OpenRouter
     ├── GitHub Models
     ├── OpenAI
     └── Other configured providers
```

> **Important:** For Claude Code, the OmniRoute base URL is `http://localhost:20128` — **do not append `/v1`** to `ANTHROPIC_BASE_URL`.

---

## 📌 Architecture

```text
                         ┌──────────────────────┐
                         │     Claude Code      │
                         │                      │
                         │       claude         │
                         └──────────┬───────────┘
                                    │
                                    │ Anthropic API
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │      OmniRoute       │
                         │                      │
                         │   localhost:20128    │
                         └──────────┬───────────┘
                                    │
                 ┌──────────────────┼──────────────────┐
                 │                  │                  │
                 ▼                  ▼                  ▼
            Anthropic          OpenRouter        GitHub Models
                 │                  │                  │
                 ▼                  ▼                  ▼
              Models             Models             Models
```

Claude Code communicates with OmniRoute locally.

OmniRoute handles the connection to the configured upstream providers.

---

# 🚀 Prerequisites

Make sure the following are installed:

* Windows 10 or Windows 11
* Node.js LTS
* npm
* Git (optional)
* Claude Code
* OmniRoute

Check Node.js:

```cmd
node -v
```

Check npm:

```cmd
npm -v
```

---

# 📦 1. Install Claude Code and OmniRoute

Open **Command Prompt (`cmd.exe`)**.

Install both packages:

```cmd
npm install -g @anthropic-ai/claude-code omniroute
```

Verify Claude Code:

```cmd
claude --version
```

Verify OmniRoute:

```cmd
omniroute --help
```

If both commands work, the installation is complete.

---

# 🌐 2. Start OmniRoute

Open **Terminal 1**.

Run:

```cmd
omniroute
```

Keep this terminal running.

OmniRoute should listen on:

```text
http://localhost:20128
```

Open the dashboard:

```text
http://localhost:20128
```

> Depending on your installed OmniRoute version, the startup command may differ. Use `omniroute --help` if necessary.

---

# 🔑 3. Configure OmniRoute

Open the OmniRoute dashboard:

```text
http://localhost:20128
```

Configure the AI providers/models that you want OmniRoute to use.

For example:

```text
Anthropic
OpenRouter
GitHub Models
OpenAI
Other supported providers
```

After configuring your providers, create an **OmniRoute API key**.

Example:

```text
OmniRoute API Key
└── <your-secret-key>
```

Keep this key private.

> Never commit the real API key to GitHub.

---

# 🧪 4. Verify OmniRoute API Access

Before configuring Claude Code, verify that OmniRoute is working.

In **Terminal 2**, use:

```cmd
curl -H "Authorization: Bearer YOUR_OMNIROUTE_API_KEY" http://localhost:20128/v1/models
```

Replace:

```text
YOUR_OMNIROUTE_API_KEY
```

with your actual OmniRoute API key.

A successful response should contain the models available through OmniRoute.

For example:

```json
{
  "data": [
    {
      "id": "anthropic/claude-opus-5"
    },
    {
      "id": "anthropic/claude-sonnet-5"
    }
  ]
}
```

The exact model names depend on your OmniRoute configuration.

---

# 🔐 5. Configure Claude Code Authentication

Claude Code needs to know that OmniRoute is the local Anthropic API endpoint.

The recommended environment variables are:

```text
ANTHROPIC_BASE_URL
ANTHROPIC_AUTH_TOKEN
```

## Recommended Configuration

In **Terminal 2**:

```cmd
set ANTHROPIC_BASE_URL=http://localhost:20128
```

Then:

```cmd
set ANTHROPIC_AUTH_TOKEN=YOUR_OMNIROUTE_API_KEY
```

Clear a normal Anthropic API key if one is already set:

```cmd
set ANTHROPIC_API_KEY=
```

Verify:

```cmd
echo %ANTHROPIC_BASE_URL%
```

Expected:

```text
http://localhost:20128
```

Verify that the token exists without printing the entire token:

```cmd
echo %ANTHROPIC_AUTH_TOKEN:~0,8%********
```

---

# ⚠️ Important: Do NOT use `/v1`

For Claude Code, use:

```text
http://localhost:20128
```

Correct:

```cmd
set ANTHROPIC_BASE_URL=http://localhost:20128
```

Incorrect:

```cmd
set ANTHROPIC_BASE_URL=http://localhost:20128/v1
```

The `/v1` path is used by API clients that directly call the OpenAI-compatible API.

Claude Code uses the Anthropic API format and adds the required API path itself.

---

# 💾 6. Permanent Windows Configuration

If you want the configuration to survive closing CMD, use `setx`.

```cmd
setx ANTHROPIC_BASE_URL "http://localhost:20128"
```

Set the OmniRoute token:

```cmd
setx ANTHROPIC_AUTH_TOKEN "YOUR_OMNIROUTE_API_KEY"
```

> Close and reopen Command Prompt after using `setx`.

Then verify:

```cmd
echo %ANTHROPIC_BASE_URL%
```

And:

```cmd
echo %ANTHROPIC_AUTH_TOKEN:~0,8%********
```

---

# 🤖 7. Model Selection

Claude Code may request a model such as:

```text
claude-opus-5
```

OmniRoute can reject this with:

```text
400 Ambiguous model 'claude-opus-5'
```

because multiple providers may expose a model with the same name.

For example:

```text
claude-opus-5
```

may be ambiguous.

A provider-qualified model is unambiguous:

```text
anthropic/claude-opus-5
```

or:

```text
gh/claude-opus-5
```

The exact provider/model identifiers depend on the models configured in OmniRoute.

Check the available models:

```cmd
curl -H "Authorization: Bearer YOUR_OMNIROUTE_API_KEY" http://localhost:20128/v1/models
```

Use an exact model ID returned by OmniRoute.

---

# 🔄 8. Automatic Model Routing

If your installed OmniRoute version provides a virtual automatic-routing model, use the exact model identifier shown by:

```cmd
curl -H "Authorization: Bearer YOUR_OMNIROUTE_API_KEY" http://localhost:20128/v1/models
```

Do **not** assume that:

```text
auto
```

or:

```text
auto/coding
```

is available on every OmniRoute version.

If your OmniRoute installation exposes:

```text
auto/coding
```

then it can be used as the Claude Code model.

For example:

```cmd
set ANTHROPIC_MODEL=auto/coding
```

If `auto/coding` is not listed by `/v1/models`, use a model ID that is actually returned by OmniRoute.

---

# 🛠️ 9. Optional: OmniRoute Claude Setup

Recent OmniRoute versions provide:

```cmd
omniroute setup-claude
```

This can generate/configure Claude Code profiles.

If OmniRoute requires authentication while connecting to itself, provide the API key:

```cmd
omniroute setup-claude --api-key "YOUR_OMNIROUTE_API_KEY"
```

If this command returns:

```text
HTTP 401 — Authentication required
```

verify that:

1. OmniRoute is running.
2. The API key is valid.
3. The key belongs to the running OmniRoute instance.
4. `/v1/models` works with the same key.

Test:

```cmd
curl -H "Authorization: Bearer YOUR_OMNIROUTE_API_KEY" http://localhost:20128/v1/models
```

If this returns the model list successfully, OmniRoute authentication is working.

---

# 📁 10. Claude Code Workspace

Recommended workspace:

```text
C:\workspace
```

Example project:

```text
C:\workspace\test
```

Create it:

```cmd
mkdir C:\workspace\test
```

Enter it:

```cmd
cd /d C:\workspace\test
```

Start Claude Code:

```cmd
claude
```

---

# 📂 11. Claude Code Local Configuration

A project can contain:

```text
C:\workspace\test
│
└── .claude
    └── settings.local.json
```

Create the directory:

```cmd
mkdir .claude
```

Create the file:

```cmd
notepad .claude\settings.local.json
```

### Important

Do not put your real OmniRoute API key into a Git-tracked configuration file.

Environment variables are preferred for secrets.

If you need a local settings file, keep it uncommitted.

---

# 🔒 12. Example Claude Configuration

For a public repository, provide an example file:

```text
.claude\settings.local.example.json
```

Example:

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "http://localhost:20128",
    "ANTHROPIC_AUTH_TOKEN": "YOUR_OMNIROUTE_API_KEY"
  }
}
```

Do not put the real key in this example file.

---

# 🚫 13. Git Security

Add this to `.gitignore`:

```gitignore
# Claude Code local configuration
.claude/settings.local.json

# Environment files
.env
.env.*

# Node
node_modules/

# Logs
*.log

# Windows
Thumbs.db
Desktop.ini

# macOS
.DS_Store
```

Check the repository before committing:

```cmd
git status
```

Make sure your real credentials are not listed.

---

# ▶️ 14. Start Claude Code

After OmniRoute is running:

### Terminal 1

```cmd
omniroute
```

### Terminal 2

```cmd
cd /d C:\workspace\test
set ANTHROPIC_BASE_URL=http://localhost:20128
set ANTHROPIC_AUTH_TOKEN=YOUR_OMNIROUTE_API_KEY
set ANTHROPIC_API_KEY=
claude
```

Do not run:

```text
/login
```

when using OmniRoute authentication.

---

# 🔄 Daily Usage

After Windows restarts, you don't need to reinstall anything.

### Terminal 1

```cmd
omniroute
```

Leave it running.

### Terminal 2

```cmd
cd /d C:\workspace\test
claude
```

If you configured the environment variables permanently using `setx`, you don't need to enter them again.

---

# 🛠️ Windows Automation Scripts

Recommended repository structure:

```text
omniroute-claude-setup/
│
├── README.md
├── .gitignore
│
├── windows/
│   ├── install.cmd
│   ├── start-omniroute.cmd
│   └── start-claude.cmd
│
└── .claude/
    └── settings.local.example.json
```

---

## `windows/install.cmd`

```bat
@echo off
title OmniRoute + Claude Code Installer

echo ==========================================
echo OmniRoute + Claude Code Installation
echo ==========================================

echo.
echo Checking Node.js...
node -v

if errorlevel 1 (
    echo Node.js is not installed.
    pause
    exit /b 1
)

echo.
echo Checking npm...
npm -v

if errorlevel 1 (
    echo npm is not available.
    pause
    exit /b 1
)

echo.
echo Installing Claude Code and OmniRoute...

call npm install -g @anthropic-ai/claude-code omniroute

if errorlevel 1 (
    echo Installation failed.
    pause
    exit /b 1
)

echo.
echo Installation completed successfully.

echo.
echo Claude Code:
claude --version

echo.
echo OmniRoute:
omniroute --help

pause
```

---

# `windows/start-omniroute.cmd`

```bat
@echo off

title OmniRoute

echo ==========================================
echo Starting OmniRoute
echo ==========================================

echo.
echo Dashboard:
echo http://localhost:20128
echo.

omniroute

pause
```

---

# `windows/start-claude.cmd`

```bat
@echo off

title Claude Code + OmniRoute

echo ==========================================
echo Claude Code + OmniRoute
echo ==========================================

cd /d "%~dp0\.."

set ANTHROPIC_BASE_URL=http://localhost:20128
set ANTHROPIC_API_KEY=

if "%ANTHROPIC_AUTH_TOKEN%"=="" (
    set /p ANTHROPIC_AUTH_TOKEN="Enter OmniRoute API Key: "
)

echo.
echo Starting Claude Code...
echo.

claude

pause
```

This script intentionally asks for the OmniRoute key instead of storing it inside the repository.

---

# 🧪 Troubleshooting

## `claude` is not recognized

Check:

```cmd
where claude
```

Then:

```cmd
npm list -g --depth=0
```

If necessary, reinstall:

```cmd
npm install -g @anthropic-ai/claude-code
```

Close and reopen CMD afterward.

---

## `omniroute` is not recognized

Check:

```cmd
where omniroute
```

Then:

```cmd
npm list -g --depth=0
```

Reinstall if necessary:

```cmd
npm install -g omniroute
```

---

## Check OmniRoute port

```cmd
netstat -ano | findstr :20128
```

Expected:

```text
TCP    127.0.0.1:20128    0.0.0.0:0    LISTENING
```

---

## Test OmniRoute

Open:

```text
http://localhost:20128
```

If the dashboard loads, OmniRoute is running.

---

## `Not logged in · Please run /login`

This normally means Claude Code is not receiving the OmniRoute authentication configuration.

Check:

```cmd
echo %ANTHROPIC_BASE_URL%
```

Expected:

```text
http://localhost:20128
```

Check the token:

```cmd
echo %ANTHROPIC_AUTH_TOKEN:~0,8%********
```

Also check:

```cmd
echo %ANTHROPIC_API_KEY%
```

If you're using OmniRoute, avoid accidentally supplying a different Anthropic API key.

Restart Claude Code after changing the variables.

---

## `HTTP 401 — Authentication required`

Test the API directly:

```cmd
curl -i -H "Authorization: Bearer YOUR_OMNIROUTE_API_KEY" http://localhost:20128/v1/models
```

If you receive:

```text
HTTP/1.1 200
```

authentication is working.

If you receive:

```text
HTTP/1.1 401
```

the API key is not being accepted by the running OmniRoute instance.

Generate/check the key in the OmniRoute dashboard.

---

## `400 Ambiguous model`

Example:

```text
API Error: 400 Ambiguous model 'claude-opus-5'
```

This means OmniRoute cannot determine which provider should handle the model.

Check available models:

```cmd
curl -H "Authorization: Bearer YOUR_OMNIROUTE_API_KEY" http://localhost:20128/v1/models
```

Then use the exact provider/model identifier returned by OmniRoute.

Example:

```text
anthropic/claude-opus-5
```

instead of:

```text
claude-opus-5
```

---

## `setup-claude` returns 401

Run:

```cmd
omniroute setup-claude --api-key "YOUR_OMNIROUTE_API_KEY"
```

If it still returns:

```text
HTTP 401 — Authentication required
```

verify:

```cmd
curl -H "Authorization: Bearer YOUR_OMNIROUTE_API_KEY" http://localhost:20128/v1/models
```

If `/v1/models` succeeds, the OmniRoute server is reachable and the API key is valid; investigate the `setup-claude` command/version next.

---

# 🔍 Useful Diagnostics

Check Claude Code:

```cmd
claude --version
```

Check Claude Code help:

```cmd
claude --help
```

Check OmniRoute:

```cmd
omniroute --version
```

Check OmniRoute help:

```cmd
omniroute --help
```

Check API:

```cmd
curl -i http://localhost:20128/v1/models
```

Check authenticated API:

```cmd
curl -i -H "Authorization: Bearer YOUR_OMNIROUTE_API_KEY" http://localhost:20128/v1/models
```

---

# 📋 Configuration Summary

| Component               | Configuration                                                                    |
| ----------------------- | -------------------------------------------------------------------------------- |
| Operating System        | Windows 10/11                                                                    |
| Claude Code             | npm global installation                                                          |
| OmniRoute               | npm global installation                                                          |
| OmniRoute Dashboard     | `http://localhost:20128`                                                         |
| Claude Code Base URL    | `http://localhost:20128`                                                         |
| OmniRoute API           | `http://localhost:20128/v1`                                                      |
| Authentication          | `ANTHROPIC_AUTH_TOKEN`                                                           |
| Claude API Key Variable | Do not use for OmniRoute unless specifically required                            |
| Workspace               | `C:\workspace`                                                                   |
| Example Project         | `C:\workspace\test`                                                              |
| Claude Project Config   | `.claude\settings.local.json`                                                    |
| Model                   | Use an exact model ID returned by OmniRoute                                      |
| Auto Routing            | Use only if the installed OmniRoute version exposes a virtual auto-routing model |

---

# 🔐 Security Checklist

Before pushing this repository to GitHub:

* [ ] No real API keys in `README.md`
* [ ] No real API keys in `.cmd` files
* [ ] No real API keys in JSON example files
* [ ] `.claude/settings.local.json` is ignored
* [ ] `.env` files are ignored
* [ ] Provider credentials are not committed
* [ ] `node_modules/` is ignored
* [ ] Previously exposed API keys have been revoked/rotated
* [ ] `git status` does not show credential files

If a secret has accidentally been committed or exposed, **rotate/revoke it immediately**.

---

# 🔗 Official OmniRoute

Official OmniRoute repository:

https://github.com/diegosouzapw/OmniRoute

Always check the official OmniRoute documentation for changes to:

* Installation commands
* Authentication
* API endpoints
* Model identifiers
* Claude Code integration
* Configuration options
* Supported providers

---

# 📝 License

This repository contains setup documentation and optional Windows helper scripts for configuring OmniRoute and Claude Code.

OmniRoute and Claude Code are separate projects and remain subject to their respective licenses and terms.
