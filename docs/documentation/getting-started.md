---
title: "Getting Started"
excerpt: "Install and configure the SIP AI Assistant with step-by-step setup instructions"
slug: getting-started
---

> 🤖 **ROBO CODED** — This documentation was made with AI and may not be 100% sane. But the code does work! 🎉

# 🚀 Getting Started

This guide walks you through setting up the SIP AI Assistant.

## 📋 Prerequisites

Before you begin, ensure you have:

| Requirement | Description |
|-------------|-------------|
| 🐳 **Docker** | Docker and Docker Compose installed |
| 📞 **SIP Server** | FreePBX, Asterisk, 3CX, or any SIP-compatible PBX |
| 🧠 **LLM Server** | OpenAI API, vLLM, Ollama, or LM Studio |
| 🎤 **Speaches** | [Speaches](https://github.com/speaches-ai/speaches) for STT/TTS |

## ⚡ Quick Start

[block:callout]
{
  "type": "info",
  "title": "Step 1: Clone the Repository",
  "body": ""
}
[/block]

```bash
git clone https://github.com/your-org/sip-agent.git
cd sip-agent
```

**Expected output:**

```
Cloning into 'sip-agent'...
remote: Enumerating objects: 1234, done.
remote: Counting objects: 100% (1234/1234), done.
remote: Compressing objects: 100% (567/567), done.
Receiving objects: 100% (1234/1234), 2.5 MiB | 10.00 MiB/s, done.
```

[block:callout]
{
  "type": "info",
  "title": "Step 2: Configure Environment",
  "body": ""
}
[/block]

```bash
cp .env.example .env
nano .env  # or use your preferred editor
```

**Minimum configuration:**

```env
# 📞 SIP Settings
SIP_USER=ai-assistant
SIP_PASSWORD=your-secure-password
SIP_DOMAIN=pbx.example.com

# 🎤 Speaches (STT + TTS)
SPEACHES_API_URL=http://speaches:8001

# 🧠 LLM Settings
LLM_BASE_URL=http://vllm:8000/v1
LLM_MODEL=openai-community/gpt2-xl
```

**GPU-Specific Model Recommendations:**

| Your GPU | LLM Model | STT Model |
|----------|-----------|-----------|
| H100 / A100 (80GB) | `meta-llama/Llama-3.1-70B-Instruct` | `faster-whisper-large-v3` |
| DGX Spark (128GB) | `meta-llama/Llama-3.1-70B-Instruct` | `faster-whisper-large-v3` |
| RTX 5090 (32GB) | `Qwen/Qwen2.5-32B-Instruct` | `faster-whisper-large-v3` |
| RTX 4090 (24GB) | `Qwen/Qwen2.5-14B-Instruct` | `faster-whisper-large-v3` |
| RTX 3090/4080 (16-24GB) | `meta-llama/Llama-3.1-8B-Instruct` | `faster-whisper-medium` |
| RTX 3080/4070 (10-12GB) | `Qwen/Qwen2.5-7B-Instruct` | `faster-whisper-small` |

> 💡 **Tip:** See [Configuration Reference](doc:configuration) for all available options and full model recommendations.

[block:callout]
{
  "type": "info",
  "title": "Step 3: Start the Services",
  "body": ""
}
[/block]

```bash
docker compose up -d
```

**Expected output:**

```
[+] Running 3/3
 ✔ Network sip-agent_default      Created
 ✔ Container speaches             Started
 ✔ Container sip-agent            Started
```

[block:callout]
{
  "type": "info",
  "title": "Step 4: Verify Installation",
  "body": ""
}
[/block]

**Health Check:**

```bash
curl http://localhost:8080/health | jq
```

**Expected output:**

```json
{
  "status": "healthy",
  "sip_registered": true,
  "active_calls": 0
}
```

**SIP Registration:**

```bash
curl http://localhost:8080/health | jq '.sip_registered'
```

**Expected output:**

```
true
```

**Available Tools:**

```bash
curl http://localhost:8080/tools | jq '.[].name'
```

**Expected output:**

```
"WEATHER"
"SET_TIMER"
"CALLBACK"
"HANGUP"
"STATUS"
"CANCEL"
"DATETIME"
"CALC"
"JOKE"
"SIMON_SAYS"
```

[block:callout]
{
  "type": "success",
  "title": "Step 5: Make a Test Call 📞",
  "body": ""
}
[/block]

1. Open your SIP phone or softphone
2. Dial the extension assigned to the assistant
3. Wait for the greeting
4. Say *"Hello!"* or *"What time is it?"*

**Example conversation:**

```
┌────────────────────────────────────────────────────────────┐
│ 📞 INCOMING CALL                                           │
├────────────────────────────────────────────────────────────┤
│ 🤖 "Hello! Welcome to the AI assistant. How can I help?"  │
│ 👤 "What time is it?"                                      │
│ 🤖 "It's 3:45 PM on Saturday, November 30th, 2025."       │
│ 👤 "Thanks!"                                               │
│ 🤖 "You're welcome! Anything else?"                       │
│ 👤 "No, goodbye"                                           │
│ 🤖 "Goodbye! Have a great day!"                           │
└────────────────────────────────────────────────────────────┘
```

## 🐳 Docker Compose Configuration

Here's a complete `docker-compose.yml`:

```yaml
services:
  # 🤖 SIP AI Assistant
  sip-agent:
    image: sip-agent:latest
    build: ./sip-agent
    container_name: sip-agent
    network_mode: host  # Required for SIP/RTP
    environment:
      - SIP_USER=${SIP_USER}
      - SIP_PASSWORD=${SIP_PASSWORD}
      - SIP_DOMAIN=${SIP_DOMAIN}
      - SPEACHES_API_URL=${SPEACHES_API_URL}
      - LLM_BASE_URL=${LLM_BASE_URL}
      - LLM_MODEL=${LLM_MODEL}
      - TEMPEST_STATION_ID=${TEMPEST_STATION_ID}
      - TEMPEST_API_TOKEN=${TEMPEST_API_TOKEN}
    volumes:
      - ./data:/app/data
    restart: unless-stopped
    depends_on:
      - speaches

  # 🎤 Speaches (STT + TTS)
  speaches:
    image: ghcr.io/speaches-ai/speaches:latest
    container_name: speaches
    ports:
      - "8001:8000"
    environment:
      - WHISPER_MODEL=Systran/faster-distil-whisper-small.en
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
    restart: unless-stopped
```

> ⚠️ **Note:** The SIP agent uses `network_mode: host` for proper SIP/RTP handling.

## 📁 Directory Structure

```
sip-agent/
├── 📂 src/
│   ├── 📄 main.py              # Application entry point
│   ├── 📄 config.py            # Configuration management
│   ├── 📄 api.py               # REST API endpoints
│   ├── 📄 sip_client.py        # SIP protocol handling
│   ├── 📄 audio_pipeline.py    # STT/TTS processing
│   ├── 📄 llm_engine.py        # LLM integration
│   ├── 📄 tool_manager.py      # Tool execution
│   └── 📂 plugins/             # Tool plugins
│       ├── 📄 weather_tool.py
│       ├── 📄 timer_tool.py
│       ├── 📄 callback_tool.py
│       └── 📄 ...
├── 📂 tools/
│   └── 📄 view-logs.py         # Log viewer utility
├── 📂 grafana/
│   └── 📂 dashboards/          # Grafana dashboards
├── 📄 docker-compose.yml
├── 📄 Dockerfile
├── 📄 .env.example
└── 📄 requirements.txt
```

## 📞 PBX Configuration

### FreePBX / Asterisk

1. Navigate to **Applications → Extensions**
2. Click **Add Extension → Add New SIP Extension**
3. Configure:
   - **User Extension:** `1000` (or your choice)
   - **Display Name:** `AI Assistant`
   - **Secret:** Your secure password
4. Click **Submit** and **Apply Config**

**Update your `.env`:**

```env
SIP_USER=1000
SIP_PASSWORD=your-extension-secret
SIP_DOMAIN=192.168.1.100  # Your PBX IP
```

### 3CX

1. Go to **Users → Add**
2. Select **Extension Type:** SIP
3. Configure authentication credentials
4. Note the extension number and password

## 🔍 Viewing Logs

### Docker Logs

```bash
docker logs -f sip-agent
```

**Example output:**

```json
{"ts": "2025-11-30 15:30:00", "level": "INFO", "event": "sip_registered", "msg": "SIP registration successful"}
{"ts": "2025-11-30 15:30:05", "level": "INFO", "event": "call_start", "msg": "Incoming call", "data": {"caller": "1001"}}
{"ts": "2025-11-30 15:30:06", "level": "INFO", "event": "stt_result", "msg": "What time is it"}
{"ts": "2025-11-30 15:30:07", "level": "INFO", "event": "llm_response", "msg": "It's 3:30 PM..."}
```

### Formatted Log Viewer

```bash
python tools/view-logs.py -f
```

**Example output:**

```
┌──────────────────────────────────────────────────────────────
│ 📞 CALL #1 - From: 1001
└──────────────────────────────────────────────────────────────
15:30:05  📞 Call started
15:30:06  👤 "What time is it?"
15:30:07  🤖 "It's 3:30 PM on Saturday, November 30th."
15:30:10  👤 "Set a timer for 5 minutes"
15:30:11  🔧 [TOOL:SET_TIMER:duration=300]
15:30:11  🤖 "Timer set for 5 minutes!"
15:30:15  📴 Call ended (duration: 0:10)
```

## 🔧 Troubleshooting

### SIP Not Registering

```bash
# Check SIP logs
docker logs sip-agent 2>&1 | grep -i "sip\|register"
```

**Common causes:**
- 🔐 Wrong credentials in `.env`
- 🔥 Firewall blocking UDP 5060
- 🌐 Wrong `SIP_DOMAIN`

### No Audio

```bash
# Test Speaches health
curl http://localhost:8001/health
```

**Expected:**
```json
{"status": "ok"}
```

```bash
# Test TTS
curl -X POST http://localhost:8001/v1/audio/speech \
  -H "Content-Type: application/json" \
  -d '{"input": "Hello world", "voice": "af_heart"}' \
  --output test.wav

# Play the audio
aplay test.wav  # Linux
afplay test.wav # macOS
```

### LLM Not Responding

```bash
# Test LLM endpoint
curl http://your-llm-server:8000/v1/models | jq
```

**Expected:**
```json
{
  "data": [
    {"id": "openai-community/gpt2-xl", "object": "model"}
  ]
}
```

## ➡️ Next Steps

- **[Configuration](doc:configuration)** — All environment variables and detailed configuration options
- **[API Reference](doc:api-reference)** — Complete REST API documentation and endpoints
- **[Built-in Tools](doc:tools)** — Explore all available AI assistant capabilities
- **[Creating Plugins](doc:plugins)** — Learn how to add custom tools and functionality
