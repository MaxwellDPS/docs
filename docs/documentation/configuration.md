---
title: "Configuration"
excerpt: "Complete configuration reference"
category:
  uri: setup
slug: configuration
---

# ⚙️ Configuration Reference

All configuration is done via environment variables. This page documents every available option.

---

## 📞 SIP Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `SIP_USER` | ✅ | `ai-assistant` | SIP account username |
| `SIP_PASSWORD` | ✅ | - | SIP account password |
| `SIP_DOMAIN` | ✅ | `localhost` | SIP server domain/IP |
| `SIP_PORT` | ❌ | `5060` | SIP server port |
| `SIP_TRANSPORT` | ❌ | `udp` | Transport: `udp`, `tcp`, `tls` |
| `SIP_REGISTRAR` | ❌ | - | Optional separate registrar |

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
| `SPEACHES_API_URL` | ✅ | `http://localhost:8001` | Speaches server URL |
| `STT_MODE` | ❌ | `batch` | `batch` or `realtime` |
| `WHISPER_MODEL` | ❌ | `Systran/faster-distil-whisper-small.en` | Whisper model |
| `WHISPER_LANGUAGE` | ❌ | `en` | Language code |

### 🎯 STT Modes

| Mode | Description | Recommended |
|------|-------------|:-----------:|
| `batch` | Buffer audio locally, send on silence | ✅ |
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
| `TTS_MODEL` | ❌ | `speaches-ai/Kokoro-82M-v1.0-ONNX` | TTS model |
| `TTS_VOICE` | ❌ | `af_heart` | Voice ID |
| `TTS_SPEED` | ❌ | `1.0` | Speech speed (0.5-2.0) |
| `TTS_RESPONSE_FORMAT` | ❌ | `wav` | Format: `wav`, `mp3`, `opus` |

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
| `LLM_BASE_URL` | ✅ | `http://vllm:8000/v1` | OpenAI-compatible API URL |
| `LLM_MODEL` | ✅ | `openai-community/gpt2-xl` | Model name |
| `LLM_API_KEY` | ❌ | `not-needed` | API key (if required) |
| `LLM_BACKEND` | ❌ | `vllm` | Backend type |
| `LLM_MAX_TOKENS` | ❌ | `512` | Max response tokens |
| `LLM_TEMPERATURE` | ❌ | `0.6` | Creativity (0.0-1.0) |
| `LLM_TOP_P` | ❌ | `0.85` | Nucleus sampling |

**Example configurations:**

```env
# 🧠 Using vLLM with openGPT
LLM_BASE_URL=http://vllm:8000/v1
LLM_MODEL=openai-community/gpt2-xl
LLM_MAX_TOKENS=512
LLM_TEMPERATURE=0.6
```

```env
# 🧠 Using OpenAI API
LLM_BASE_URL=https://api.openai.com/v1
LLM_MODEL=gpt-4
LLM_API_KEY=sk-your-api-key
```

```env
# 🧠 Using Ollama
LLM_BASE_URL=http://ollama:11434/v1
LLM_MODEL=llama3.1
```

---

## 🎚️ Audio & VAD Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `MIN_SPEECH_DURATION_MS` | ❌ | `200` | Min speech to process (ms) |
| `MAX_SPEECH_DURATION_S` | ❌ | `10.0` | Max utterance length (s) |
| `SILENCE_TIMEOUT_MS` | ❌ | `750` | Silence before end-of-speech |
| `BARGE_IN_MIN_DURATION` | ❌ | `400` | Min duration to interrupt (ms) |
| `BARGE_IN_ENERGY_THRESHOLD` | ❌ | `2000` | Energy threshold |

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
| `MAX_CONVERSATION_TURNS` | ❌ | `10` | Max turns before ending |
| `CALLBACK_RING_TIMEOUT` | ❌ | `30` | Callback ring timeout (s) |

---

## 🌤️ Weather (Tempest) Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `TEMPEST_STATION_ID` | ❌ | - | WeatherFlow station ID |
| `TEMPEST_API_TOKEN` | ❌ | - | WeatherFlow API token |

**Get your credentials:**

1. Go to [tempestwx.com](https://tempestwx.com/)
2. Navigate to **Settings → Data Authorizations**
3. Create a new token
4. Find your station ID in the URL

![Tempest API settings](screenshots/tempest-api.png)
<!-- TODO: Screenshot of Tempest API token page -->

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
| `API_RETRY_ATTEMPTS` | ❌ | `3` | Retry attempts |
| `API_RETRY_BASE_DELAY_S` | ❌ | `0.5` | Base retry delay |
| `API_RETRY_MAX_DELAY_S` | ❌ | `5.0` | Max retry delay |
| `API_TIMEOUT_S` | ❌ | `30.0` | Request timeout |

---

## 📊 Telemetry Settings

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `LOG_LEVEL` | ❌ | `INFO` | `DEBUG`, `INFO`, `WARNING`, `ERROR` |
| `OTEL_ENABLED` | ❌ | `true` | Enable OpenTelemetry |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | ❌ | `http://otel-collector:4317` | OTLP endpoint |
| `OTEL_SERVICE_NAME` | ❌ | `sip-agent` | Service name |

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
| `DATA_DIR` | ❌ | `./data` | Persistent data directory |
| `REDIS_URL` | ❌ | `redis://localhost:6379/0` | Redis URL |

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
```

---

## 📊 Grafana Dashboard

Import the included dashboard for monitoring:

```bash
# Dashboard JSON location
grafana/dashboards/sip-agent.json
```

![Grafana dashboard](screenshots/grafana-dashboard.png)
<!-- TODO: Screenshot of Grafana dashboard -->

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