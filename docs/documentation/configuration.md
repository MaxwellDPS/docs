---
title: "Configuration"
excerpt: "Complete configuration reference"
slug: configuration
---

> 🤖 **ROBO CODED** — This documentation was made with AI and may not be 100% sane. But the code does work! 🎉

# ⚙️ Configuration Reference

All configuration is done via environment variables. This page documents every available option.

---

## 📞 SIP Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `SIP_USER` | Yes | `ai-assistant` | SIP account username |
| `SIP_PASSWORD` | Yes | - | SIP account password |
| `SIP_DOMAIN` | Yes | `localhost` | SIP server domain/IP |
| `SIP_PORT` | No | `5060` | SIP server port |
| `SIP_TRANSPORT` | No | `udp` | Transport: `udp`, `tcp`, `tls` |
| `SIP_REGISTRAR` | No | - | Optional separate registrar |

**Example:**

```env
# 📞 SIP Connection
SIP_USER=ai-assistant
SIP_PASSWORD=super-secret-password
SIP_DOMAIN=pbx.example.com
SIP_PORT=5060
SIP_TRANSPORT=udp
```

---

## 🎤 Speaches Settings (STT + TTS)

This project uses [Speaches](https://github.com/speaches-ai/speaches) as a unified speech server.

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `SPEACHES_API_URL` | Yes | `http://localhost:8001` | Speaches server URL |
| `STT_MODE` | No | `batch` | `batch` or `realtime` |
| `WHISPER_MODEL` | No | `Systran/faster-distil-whisper-small.en` | Whisper model |
| `WHISPER_LANGUAGE` | No | `en` | Language code |

### 🎯 STT Modes

| Mode | Description | Recommended |
|------|-------------|:-----------:|
| `batch` | Buffer audio locally, send on silence | Yes |
| `realtime` | Stream continuously to server | ⚠️ Experimental |

**Example:**

```env
# 🎤 Speech Recognition
SPEACHES_API_URL=http://speaches:8001
STT_MODE=batch
WHISPER_MODEL=Systran/faster-distil-whisper-small.en
WHISPER_LANGUAGE=en
```

---

## 🔊 TTS Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `TTS_MODEL` | No | `speaches-ai/Kokoro-82M-v1.0-ONNX` | TTS model |
| `TTS_VOICE` | No | `af_heart` | Voice ID |
| `TTS_SPEED` | No | `1.0` | Speech speed (0.5-2.0) |
| `TTS_RESPONSE_FORMAT` | No | `wav` | Format: `wav`, `mp3`, `opus` |

**Example:**

```env
# 🔊 Text-to-Speech
TTS_MODEL=speaches-ai/Kokoro-82M-v1.0-ONNX
TTS_VOICE=af_heart
TTS_SPEED=1.0
TTS_RESPONSE_FORMAT=wav
```

**Available voices:**

```
af_heart    - American Female (warm)
af_bella    - American Female (professional)
am_adam     - American Male (casual)
am_michael  - American Male (professional)
bf_emma     - British Female
bm_george   - British Male
```

---

## 🧠 LLM Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `LLM_BASE_URL` | Yes | `http://vllm:8000/v1` | OpenAI-compatible API URL |
| `LLM_MODEL` | Yes | `openai-community/gpt2-xl` | Model name |
| `LLM_API_KEY` | No | `not-needed` | API key (if required) |
| `LLM_BACKEND` | No | `vllm` | Backend type |
| `LLM_MAX_TOKENS` | No | `512` | Max response tokens |
| `LLM_TEMPERATURE` | No | `0.6` | Creativity (0.0-1.0) |
| `LLM_TOP_P` | No | `0.85` | Nucleus sampling |

**Example configurations:**

```env
# Using vLLM with openGPT
LLM_BASE_URL=http://vllm:8000/v1
LLM_MODEL=openai-community/gpt2-xl
LLM_MAX_TOKENS=512
LLM_TEMPERATURE=0.6
```

```env
# Using OpenAI API
LLM_BASE_URL=https://api.openai.com/v1
LLM_MODEL=gpt-4
LLM_API_KEY=sk-your-api-key
```

```env
# Using Ollama
LLM_BASE_URL=http://ollama:11434/v1
LLM_MODEL=llama3.1
```

---

## 🎮 Recommended Models by GPU

### NVIDIA H100 / A100 (80GB HBM)

Data center GPUs with maximum performance.

| Component | Model | Notes |
|-----------|-------|-------|
| LLM | `meta-llama/Llama-3.1-70B-Instruct` | Best quality |
| LLM | `Qwen/Qwen2.5-72B-Instruct` | Alternative |
| STT | `Systran/faster-whisper-large-v3` | Best accuracy |
| TTS | `af_heart` | Warm voice |

```env
LLM_MODEL=meta-llama/Llama-3.1-70B-Instruct
STT_MODEL=Systran/faster-whisper-large-v3
TTS_VOICE=af_heart
```

### NVIDIA DGX Spark (128GB Unified)

Grace Blackwell GB10 with shared CPU/GPU memory.

| Component | Model | Notes |
|-----------|-------|-------|
| LLM | `meta-llama/Llama-3.1-70B-Instruct` | Fits unified memory |
| LLM | `deepseek-ai/DeepSeek-R1-Distill-Llama-70B` | Reasoning focused |
| STT | `Systran/faster-whisper-large-v3` | Best accuracy |
| TTS | `af_heart` | Warm voice |

```env
LLM_MODEL=meta-llama/Llama-3.1-70B-Instruct
STT_MODEL=Systran/faster-whisper-large-v3
TTS_VOICE=af_heart
```

### NVIDIA RTX 5090 (32GB GDDR7)

Next-gen consumer flagship.

| Component | Model | Notes |
|-----------|-------|-------|
| LLM | `Qwen/Qwen2.5-32B-Instruct` | Best for 32GB |
| LLM | `mistralai/Mistral-Small-24B-Instruct-2501` | Good balance |
| STT | `Systran/faster-whisper-large-v3` | Best accuracy |
| TTS | `af_heart` | Warm voice |

```env
LLM_MODEL=Qwen/Qwen2.5-32B-Instruct
STT_MODEL=Systran/faster-whisper-large-v3
TTS_VOICE=af_heart
```

### NVIDIA RTX 4090 (24GB GDDR6X)

Current consumer flagship.

| Component | Model | Notes |
|-----------|-------|-------|
| LLM | `Qwen/Qwen2.5-14B-Instruct` | Best for 24GB |
| LLM | `meta-llama/Llama-3.1-8B-Instruct` | Faster |
| STT | `Systran/faster-whisper-large-v3` | Best accuracy |
| TTS | `af_heart` | Warm voice |

```env
LLM_MODEL=Qwen/Qwen2.5-14B-Instruct
STT_MODEL=Systran/faster-whisper-large-v3
TTS_VOICE=af_heart
```

### NVIDIA RTX 3090 / 4080 (16-24GB)

High-end consumer GPUs.

| Component | Model | Notes |
|-----------|-------|-------|
| LLM | `meta-llama/Llama-3.1-8B-Instruct` | Best for 16-24GB |
| LLM | `Qwen/Qwen2.5-7B-Instruct` | Faster |
| STT | `Systran/faster-whisper-medium` | Good balance |
| TTS | `af_heart` | Warm voice |

```env
LLM_MODEL=meta-llama/Llama-3.1-8B-Instruct
STT_MODEL=Systran/faster-whisper-medium
TTS_VOICE=af_heart
```

### NVIDIA RTX 3080 / 4070 (10-12GB)

Mid-range GPUs.

| Component | Model | Notes |
|-----------|-------|-------|
| LLM | `Qwen/Qwen2.5-7B-Instruct` | Best for 10-12GB |
| LLM | `microsoft/Phi-3-mini-4k-instruct` | Very fast |
| STT | `Systran/faster-whisper-small` | Low VRAM |
| TTS | `af_heart` | Warm voice |

```env
LLM_MODEL=Qwen/Qwen2.5-7B-Instruct
STT_MODEL=Systran/faster-whisper-small
TTS_VOICE=af_heart
```

### Low-Latency Stack (Any GPU)

Optimized for fastest response times.

```env
LLM_MODEL=Qwen/Qwen2.5-3B-Instruct
STT_MODEL=Systran/faster-whisper-tiny.en
TTS_VOICE=af_heart
TTS_SPEED=1.1
```

### TTS Voice Options

| Voice | Style | Gender | Accent |
|-------|-------|--------|--------|
| `af_heart` | Warm, friendly | Female | American |
| `af_bella` | Professional | Female | American |
| `af_sarah` | Casual | Female | American |
| `am_adam` | Neutral | Male | American |
| `am_michael` | Professional | Male | American |
| `bf_emma` | Warm | Female | British |
| `bm_george` | Professional | Male | British |

---

## 🎚️ Audio & VAD Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `MIN_SPEECH_DURATION_MS` | No | `200` | Min speech to process (ms) |
| `MAX_SPEECH_DURATION_S` | No | `10.0` | Max utterance length (s) |
| `SILENCE_TIMEOUT_MS` | No | `750` | Silence before end-of-speech |
| `BARGE_IN_MIN_DURATION` | No | `400` | Min duration to interrupt (ms) |
| `BARGE_IN_ENERGY_THRESHOLD` | No | `2000` | Energy threshold |

**Example:**

```env
# 🎚️ Audio Processing
MIN_SPEECH_DURATION_MS=200
MAX_SPEECH_DURATION_S=10.0
SILENCE_TIMEOUT_MS=750
BARGE_IN_MIN_DURATION=400
BARGE_IN_ENERGY_THRESHOLD=2000
```

---

## 💬 Conversation Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `MAX_CONVERSATION_TURNS` | No | `10` | Max turns before ending |
| `CALLBACK_RING_TIMEOUT` | No | `30` | Callback ring timeout (s) |

---

## 🌤️ Weather (Tempest) Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `TEMPEST_STATION_ID` | No | - | WeatherFlow station ID |
| `TEMPEST_API_TOKEN` | No | - | WeatherFlow API token |

**Get your credentials:**

1. Go to [tempestwx.com](https://tempestwx.com/)
2. Navigate to **Settings → Data Authorizations**
3. Create a new token
4. Find your station ID in the URL

![Tempest API settings](screenshots/tempest-api.png)

**Example:**

```env
# 🌤️ Weather Station
TEMPEST_STATION_ID=12345
TEMPEST_API_TOKEN=a1b2c3d4-e5f6-7890-abcd-ef1234567890
```

---

## 🔄 API Retry Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `API_RETRY_ATTEMPTS` | No | `3` | Retry attempts |
| `API_RETRY_BASE_DELAY_S` | No | `0.5` | Base retry delay |
| `API_RETRY_MAX_DELAY_S` | No | `5.0` | Max retry delay |
| `API_TIMEOUT_S` | No | `30.0` | Request timeout |

---

## 📊 Telemetry Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `LOG_LEVEL` | No | `INFO` | `DEBUG`, `INFO`, `WARNING`, `ERROR` |
| `OTEL_ENABLED` | No | `true` | Enable OpenTelemetry |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | No | `http://otel-collector:4317` | OTLP endpoint |
| `OTEL_SERVICE_NAME` | No | `sip-agent` | Service name |

**Example:**

```env
# 📊 Logging & Telemetry
LOG_LEVEL=INFO
OTEL_ENABLED=true
OTEL_EXPORTER_OTLP_ENDPOINT=http://otel-collector:4317
OTEL_SERVICE_NAME=sip-agent
```

---

## 💾 Storage Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `DATA_DIR` | No | `./data` | Persistent data directory |
| `REDIS_URL` | No | `redis://localhost:6379/0` | Redis URL |

---

## 🗣️ Phrases Configuration

Customize the assistant's pre-generated phrases for greetings, goodbyes, acknowledgments, and more.

### Configuration Methods

**Method 1: Environment Variables (JSON array)**

```env
PHRASES_GREETINGS=["Hello! How can I help?","Hi there!","Hey!"]
PHRASES_GOODBYES=["Goodbye!","Take care!","See ya!"]
```

**Method 2: Environment Variables (comma-separated)**

```env
PHRASES_GREETINGS=Hello! How can I help?,Hi there!,Hey!
PHRASES_GOODBYES=Goodbye!,Take care!,See ya!
```

**Method 3: JSON File** (recommended for complex setups)

Create `data/phrases.json`:

```json
{
  "greetings": [
    "Hello! How can I help you today?",
    "Hi there! What can I do for you?",
    "Hey! What do you need?"
  ],
  "goodbyes": [
    "Goodbye!",
    "Take care!",
    "Have a great day!"
  ],
  "acknowledgments": [
    "Okay.",
    "Got it.",
    "One moment.",
    "Sure.",
    "Copy that."
  ],
  "thinking": [
    "Let me check.",
    "One moment.",
    "Working on it."
  ],
  "errors": [
    "Sorry, I didn't catch that.",
    "Could you repeat that please?",
    "I didn't quite get that."
  ],
  "followups": [
    "Is there anything else I can help with?",
    "Can I help with anything else?",
    "Anything else?"
  ],
  "precache_extra": [
    "Hello",
    "Goodbye",
    "Yes",
    "No",
    "Thank you"
  ]
}
```

### Phrase Categories

| Variable | Category | Description |
|----------|----------|-------------|
| `PHRASES_GREETINGS` | 👋 Greetings | Played when call is answered |
| `PHRASES_GOODBYES` | 👋 Goodbyes | Played when ending call |
| `PHRASES_ACKNOWLEDGMENTS` | ✅ Acknowledgments | Quick responses while processing |
| `PHRASES_THINKING` | 🤔 Thinking | Played while waiting for LLM |
| `PHRASES_ERRORS` | ❌ Errors | Played when speech not understood |
| `PHRASES_FOLLOWUPS` | 🔄 Follow-ups | Played after completing a task |
| `PHRASES_PRECACHE` | ⚡ Pre-cache | Additional phrases to pre-synthesize |

### Example: Custom Personality

**Friendly Assistant:**

```json
{
  "greetings": [
    "Hey there, friend! What can I do for you?",
    "Hello! I'm so happy to help!",
    "Hi! Ready when you are!"
  ],
  "goodbyes": [
    "Take care! Talk soon!",
    "Bye bye! Have an awesome day!",
    "See you later, alligator!"
  ]
}
```

**Professional Assistant:**

```json
{
  "greetings": [
    "Good day. How may I assist you?",
    "Hello. What can I help you with today?",
    "Greetings. Please state your request."
  ],
  "goodbyes": [
    "Thank you for calling. Goodbye.",
    "Have a pleasant day. Goodbye.",
    "Thank you. Take care."
  ]
}
```

**Sassy Robot:**

```json
{
  "greetings": [
    "Beep boop! What do you want, human?",
    "State your business, meatbag!",
    "Oh great, another call. What is it?"
  ],
  "goodbyes": [
    "Finally! Goodbye!",
    "Don't let the door hit you!",
    "Bye! Try not to miss me too much!"
  ],
  "errors": [
    "Did you just make a sound? Try again.",
    "My audio sensors must be malfunctioning.",
    "I'm sorry, I don't speak mumble."
  ]
}
```

### Pre-caching Behavior

All configured phrases are automatically pre-synthesized at startup for instant playback:

```
┌─────────────────────────────────────────────────────────────┐
│ 🚀 Startup                                                  │
├─────────────────────────────────────────────────────────────┤
│ 📄 Load phrases from config                                 │
│ 🎤 Pre-synthesize all phrases via TTS                      │
│ 💾 Cache audio in memory                                    │
│ ⚡ Ready for instant playback!                              │
└─────────────────────────────────────────────────────────────┘
```

**Startup log:**

```
INFO: Pre-caching 25 phrases...
INFO: Cached 25 phrases
INFO: Speaches TTS ready, 25 phrases cached
```

---

## 📋 Complete Example

```env
# =============================================================================
# 📞 SIP AI Assistant - Complete Configuration
# =============================================================================

# ──────────────────────────────────────────────────────────────────────────────
# 📞 SIP Connection
# ──────────────────────────────────────────────────────────────────────────────
SIP_USER=ai-assistant
SIP_PASSWORD=super-secret-password
SIP_DOMAIN=pbx.example.com
SIP_PORT=5060
SIP_TRANSPORT=udp

# ──────────────────────────────────────────────────────────────────────────────
# 🎤 Speaches (STT + TTS)
# ──────────────────────────────────────────────────────────────────────────────
SPEACHES_API_URL=http://speaches:8001
STT_MODE=batch
WHISPER_MODEL=Systran/faster-distil-whisper-small.en
WHISPER_LANGUAGE=en
TTS_MODEL=speaches-ai/Kokoro-82M-v1.0-ONNX
TTS_VOICE=af_heart
TTS_SPEED=1.0

# ──────────────────────────────────────────────────────────────────────────────
# 🧠 LLM (Language Model)
# ──────────────────────────────────────────────────────────────────────────────
LLM_BASE_URL=http://vllm:8000/v1
LLM_MODEL=openai-community/gpt2-xl
LLM_MAX_TOKENS=512
LLM_TEMPERATURE=0.6
LLM_TOP_P=0.85

# ──────────────────────────────────────────────────────────────────────────────
# 🎚️ Audio Processing
# ──────────────────────────────────────────────────────────────────────────────
MIN_SPEECH_DURATION_MS=200
MAX_SPEECH_DURATION_S=10.0
SILENCE_TIMEOUT_MS=750
BARGE_IN_MIN_DURATION=400
BARGE_IN_ENERGY_THRESHOLD=2000

# ──────────────────────────────────────────────────────────────────────────────
# 💬 Conversation
# ──────────────────────────────────────────────────────────────────────────────
MAX_CONVERSATION_TURNS=10
CALLBACK_RING_TIMEOUT=30

# ──────────────────────────────────────────────────────────────────────────────
# 🌤️ Weather Station (Optional)
# ──────────────────────────────────────────────────────────────────────────────
TEMPEST_STATION_ID=12345
TEMPEST_API_TOKEN=your-api-token

# ──────────────────────────────────────────────────────────────────────────────
# 📊 Logging & Telemetry
# ──────────────────────────────────────────────────────────────────────────────
LOG_LEVEL=INFO
OTEL_ENABLED=true
OTEL_EXPORTER_OTLP_ENDPOINT=http://otel-collector:4317
OTEL_SERVICE_NAME=sip-agent

# ──────────────────────────────────────────────────────────────────────────────
# 💾 Storage
# ──────────────────────────────────────────────────────────────────────────────
DATA_DIR=./data

# ──────────────────────────────────────────────────────────────────────────────
# 🗣️ Phrases (Optional - or use data/phrases.json)
# ──────────────────────────────────────────────────────────────────────────────
# PHRASES_GREETINGS=["Hello! How can I help?","Hi there!","Hey!"]
# PHRASES_GOODBYES=["Goodbye!","Take care!","Have a great day!"]
# PHRASES_ACKNOWLEDGMENTS=["Okay.","Got it.","One moment."]
# PHRASES_THINKING=["Let me check.","One moment."]
# PHRASES_ERRORS=["Sorry, I didn't catch that.","Could you repeat that?"]
# PHRASES_FOLLOWUPS=["Anything else?","Can I help with anything else?"]
```

---

## 📊 Grafana Dashboard

Import the included dashboard for monitoring:

```bash
# Dashboard JSON location
grafana/dashboards/sip-agent.json
```

![Grafana dashboard](screenshots/grafana-dashboard.png)

**Metrics available:**

```
┌─────────────────────────────────────────────────────────────┐
│ 📊 SIP Agent Dashboard                                      │
├─────────────────────────────────────────────────────────────┤
│ 📞 Active Calls: 1                                          │
│ 📈 Total Calls Today: 47                                    │
│ ⏱️ Avg Call Duration: 2m 34s                                │
│ 🎤 STT Latency (p95): 245ms                                │
│ 🔊 TTS Latency (p95): 180ms                                │
│ 🧠 LLM Latency (p95): 890ms                                │
│ 🔧 Tool Executions: 23                                     │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔐 Secrets Management

### Docker Secrets

```yaml
# docker-compose.yml
services:
  sip-agent:
    secrets:
      - sip_password
      - llm_api_key
    environment:
      - SIP_PASSWORD_FILE=/run/secrets/sip_password
      - LLM_API_KEY_FILE=/run/secrets/llm_api_key

secrets:
  sip_password:
    file: ./secrets/sip_password.txt
  llm_api_key:
    file: ./secrets/llm_api_key.txt
```

### Environment Variable Precedence

```
1. 🥇 Direct environment variables (docker run -e)
2. 🥈 .env file in working directory
3. 🥉 Default values
```
