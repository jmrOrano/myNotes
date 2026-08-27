# Setting Up Local AI Agent

August 05, 2026

Official Documentation for reference: https://docs.ollama.com/integrations/claude-code

---

## Overview

An attempt to run a smaller AI model for agentic tool.
64-bit operating system, x64-based processor

`Machine` - Laptop
`RAM` - 16GB (8+8 3200Mhz)
`GPU` - RTX 3050Ti(4GB VRAM DDR6)
`CPU` - Ryzen 5 5600H
`OS` - Windows 11 Home 

---

## Abstract Architecture

```
┌─────────────────────────┐
│  HARDWARE (RTX 3050Ti)  │  ← physical ceiling
└────────────┬─────────────┘
             ▼┌─────────────────────────┐
│        OLLAMA            │  ← host/server
└────────────┬─────────────┘
             ▼
┌─────────────────────────┐
│     THE MODEL           │  ← brain
│  e.g. Qwen2.5-Coder 3B  | 
└────────────┬────────────┘
             ▼
┌─────────────────────────┐
│  CONNECTOR (env vars)     │  ← usapan/agreement
└────────────┬─────────────┘
             ▼
┌─────────────────────────┐
│     AI AGENT (Aider, Claude Code, etc)│  ← agent/orchestrator
│  reads files, plans,      │
│  calls tools, loops       │
└──┬──────────┬──────────┬──┘
   │          │          │
   ▼          ▼          ▼
┌──────┐  ┌────────────┐ ┌──────────────┐
│WORKSPACE│ │ RAG LAYER  │ │ WEB SEARCH   │  ← "extra tools to call when need"
│ (repo/  │ │ Obsidian   │ │ tool         │     
│  files) │ │ vault →    │ │ (SearXNG/    │     
│         │ │ embeddings │ │  Brave API)  │     
│         │ │ → vector DB│ │ → trimmed    │
│         │ │ → relevant │ │   results    │
│         │ │   chunks   │ │              │
└──────┘  └────────────┘ └──────────────┘
```

---

## Prerequisites and Installations

### Install Ollama at:
```
Ollama - https://ollama.com/
```

### (Disregarded) Install Claude Code native at:
This approach no longer an option due to hardware limitation
```
https://code.claude.com/docs/en/overview#terminal
```
During the installation a setup note will appear:
```
‼ Setup notes:
  ● Native installation exists but C:\Users\<youruser>\.local\bin is not in your PATH. Add it by opening: System Properties →
    Environment Variables → Edit User PATH → New → Add the path above. Then restart your terminal.
```
Fix this by changing environment variables:
```
Go to Environment Variables 
Under the 'System Variables' ---> Find the variable name "Path"
Click the "Path" variable and edit. 
Then add an entry C:\Users\<youruser>\.local\bin
```
Verify by running:
```
claude --version
```

**Find a model at:**
```
https://ollama.com/search
```
Run the following to pull:
```
ollama pull qwen3.5:4b
```

### Aider Installation
At: https://aider.chat/docs/install.html

---

## Set Environment Variables

### For Claude Code
Setting the environment variables is necessary if dont plan to use claude API key, or else: 
```
PS F:\> claude --debug --model qwen3.5:4b
 ▎ Debug mode enabled · logging to C:\Users\<youruser>\.claude\debug\34b94245-6d14-450c-8a09-27109dfdc806.txt
  Press Ctrl-C again to exit                                                                Not logged in · Run /login
                                                                                                                 Debug
```

>Open Environment variable
```
sysdm.cpl
```
 Add new:
 ```
Variable name: ANTHROPIC_BASE_URL 
value: http://localhost:11434 
→ OK
 ```

Add new:
```
Variable name: ANTHROPIC_AUTH_TOKEN 
Variable value: ollama
```

>Or
>directly add the ff in the file at
```
~/.claude/settings.json
```

```
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "ollama",
    "ANTHROPIC_API_KEY": "",
    "ANTHROPIC_BASE_URL": "http://localhost:11434"
  }
}
```

### For Aider

> Powershell Terminal/env var (Temporary session)
```
$env:OLLAMA_API_BASE = "http://127.0.0.1:11434"
```

---

## Testing

Declare the env var first: 
```
#POWERSHELL
$env:OLLAMA_API_BASE = "http://127.0.0.1:11434"

#CMD
set OLLAMA_API_BASE="http://127.0.0.1:11434"
```

Go to working dir and Run:
```
aider --model ollama_chat/qwen2.5-coder:3b 
```

---

## Uninstalling Claude Code

**Native installation**

>Run powershell as admin and kill the processes first:
```
taskkill /F /IM claude.exe
```

Then:
```
Remove-Item -Path "$env:USERPROFILE\.local\bin\claude.exe" -Force
Remove-Item -Path "$env:USERPROFILE\.local\share\claude" -Recurse -Force
```

Then verify:
```
claude --version
```

---

## Setting up Claude Desktop to use MCP to edit files

August 06, 2026

Video guide at: https://www.youtube.com/watch?v=uDRjhVXZaBc

### Prerequisite

**Desktop Commander MCP**
Repo at: https://github.com/wonderwhy-er/desktopcommandermcp#how-to-install

**NodeJs** 
Link: https://nodejs.org/en/download

### Editing the Claude Desktop config
Inside the C:\Users\<user>\AppData\Roaming\Claude\claude_desktop_config.json
```
{
  "mcpServers": {
    "desktop-commander": {
      "command": "npx",
      "args": [
        "-y",
        "@wonderwhy-er/desktop-commander@latest"
      ]
    }
  }
}
```

or you can just copy and paste this in command prompt:
```
npx @wonderwhy-er/desktop-commander@latest setup
```

---

## Testing Results & Findings (Aug 5-6, 2026)

### Claude Code + Ollama
Tested models: qwen3.5:4b, phi4-mini (sam860/phi4-mini:3.8b)

**Issues encountered:**
- qwen3.5:4b — reasoning model, "Thinking..." step caused 5+ min response
  times even for simple prompts; 52%/48% CPU/GPU split (VRAM insufficient
  even at default 4096 context)
- Both models hallucinated identity/output ("Opus 5" mention, unrelated
  HTML-entity/permutation response to a simple "list files" request) —
  model got confused by Claude Code's complex system prompt (agents,
  worktree, tool schemas)
- Ollama itself warns against qwen2.5-coder for Claude Code:
  "does not work well with Claude Code. Try an agent-capable model like
  glm-5.2:cloud or gemma4:12b instead" — both too large for 4GB VRAM

**Conclusion:** Tool-calling (JSON function-calling schema) appears
unreliable on 3-4B models at 4GB VRAM.

### Aider + Ollama
Model: qwen2.5-coder:3b, edit-format diff

**Issues encountered:**
- Default "whole" edit format: model output literal placeholder
  boilerplate from Aider's own system prompt example
  (`/path/to/your_directory/file1.js`) instead of real file content —
  identical output regardless of the actual prompt
- Switched to `--edit-format diff`, still failed to conform to edit
  format on `/code` (direct edit) command — triggered Aider's automatic
  retry loop, compounding context growth each retry → 30-60+ min hang,
  had to Ctrl+C
- `/ask` (read-only Q&A) mode worked correctly and reliably — because
  Aider itself switches the system prompt deterministically (no format
  decision left to the model)

**Conclusion:** Diff-based editing still too complex for 3B model to
conform to reliably; read-only Q&A mode is the reliable ceiling for
this hardware.

### Overall Conclusion
On RTX 3050Ti 4GB VRAM, local models (3-4B class) can chat/explain/
review reliably, but cannot reliably perform autonomous agentic file
editing — neither via JSON tool-calling (Claude Code) nor via diff-based
editing (Aider, /code mode). This appears to be a genuine capability
ceiling at this model size/VRAM tier, not a configuration issue.

**Decision:** For the Obsidian notes "thinking buddy" use case, switched
to Claude Desktop + Desktop Commander MCP (read-write filesystem access)
instead of the fully-local agentic setup — trades "free/local" for
reliability. `allowedDirectories` restricted to `F:\ObsidianNotes` only
as a safety measure (default is full filesystem access).
