# Kaggle Ollama Setup Guide

This project provides a complete, working solution to install, configure, and run **Ollama** within Kaggle notebooks or restricted Linux environments where systemd is not available. It includes both shell-based and pure Python integration methods to pull and run large language models (LLMs) like `llama3.2:1b`.

## Features
- 📦 **Zero-Configuration Install**: Automated system package tracking (`zstd`, `curl`, `wget`).
- 🚀 **Systemd-less Server Daemon**: Background server execution using `subprocess` or `os.system` mapping around systemd limitations.
- 🐍 **Dual Implementation**: Choose between direct Command-Line Interface (CLI) execution or native Python API wrappers (`ollama-python`).
- ⚡ **Minimalist Workflow**: Includes a one-liner installation snippet for fast deployments.

---

## Installation & Setup Options

### Option 1: Step-by-Step CLI Setup (Recommended)

1. **Install required system tools:**
   ```bash
   apt-get update -qq && apt-get install -y zstd curl wget -qq
   ```
2. **Download and install the official Ollama binary:**
   ```bash
   curl -fsSL https://ollama.com/install.sh | sh
   ```
3. **Initialize the background server process:**
   ```python
   import subprocess
   import time
   
   # Starts the server in a non-blocking background task
   subprocess.Popen(['ollama', 'serve'], stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
   time.sleep(8) # Wait for initialization
   ```

### Option 2: Pure Python Integration

Install the official Python bindings and manage models programmatically:

```bash
pip install ollama
```

```python
import os
import time
import ollama

# Start server daemon
os.system('ollama serve &')
time.sleep(10)

# Pull and query a model via Python Client
client = ollama.Client(host='http://localhost:11434')
client.pull('llama3.2:1b')

response = client.chat(
    model='llama3.2:1b',
    messages=[{'role': 'user', 'content': 'What is 2+2? Answer briefly.'}]
)
print(response['message']['content'])
```

### Option 3: One-Liner Quickstart

```python
import subprocess, time
!apt-get install -y zstd -qq && curl -fsSL https://ollama.com/install.sh | sh
subprocess.Popen(['ollama', 'serve'], stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
time.sleep(10)
!ollama pull llama3.2:1b
!ollama run llama3.2:1b "Say hello in 5 words"
```

---

## Troubleshooting

- **Address Already in Use (`bind: address already in use`)**: This occurs if the server is executed multiple times. The script handles it transparently, or you can kill conflicting processes using `pkill ollama`.
- **Systemd Warning**: You will see a `WARNING: systemd is not running` prompt during installation. This is expected behavior in container environments; the background workers circumvent this requirement.
