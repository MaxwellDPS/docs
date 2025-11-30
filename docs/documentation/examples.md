---
title: "Examples"
excerpt: "Common use cases and integration examples"
category:
  uri: guides
slug: examples
---

> 🤖 **ROBO CODED** — This documentation was made with AI and may not be 100% sane.

# 📖 Examples

Real-world examples and integration patterns for the SIP AI Assistant.

![Integrations overview](screenshots/integrations.png)
<!-- TODO: Diagram showing integrations -->

---

## 🌅 Morning Weather Briefing

Schedule a daily weather call at 7am:

```bash
curl -X POST http://sip-agent:8080/schedule \
  -H "Content-Type: application/json" \
  -d '{
    "extension": "5551234567",
    "tool": "WEATHER",
    "at_time": "07:00",
    "timezone": "America/Los_Angeles",
    "recurring": "daily",
    "prefix": "Good morning! Here is your weather update for today.",
    "suffix": "Have a great day!"
  }' | jq
```

**Response:**

```json
{
  "schedule_id": "a1b2c3d4",
  "status": "scheduled",
  "extension": "5551234567",
  "scheduled_for": "2025-12-01T07:00:00-08:00",
  "delay_seconds": 28800,
  "message": "Call scheduled for 2025-12-01T07:00:00-08:00",
  "recurring": "daily"
}
```

**What happens at 7am:**

```
┌─────────────────────────────────────────────────────────────┐
│ ⏰ 07:00 AM - Scheduled call triggered                      │
├─────────────────────────────────────────────────────────────┤
│ 📞 Dialing 5551234567...                                   │
│ 🔔 Ring... ring...                                         │
│ 📱 Call answered                                            │
│ 🔊 "Good morning! Here is your weather update for today."  │
│ 🔊 "At Storm Lake, as of 7:00 am, it's 38 degrees..."     │
│ 🔊 "Have a great day!"                                     │
│ 📴 Call ended                                               │
│ 🔄 Rescheduled for tomorrow at 07:00                       │
└─────────────────────────────────────────────────────────────┘
```

![Scheduled weather call](screenshots/scheduled-weather.png)
<!-- TODO: Screenshot of log viewer showing scheduled call -->

---

## 📅 Appointment Reminders

Send appointment reminders with confirmation:

```bash
curl -X POST http://sip-agent:8080/call \
  -H "Content-Type: application/json" \
  -d '{
    "extension": "5551234567",
    "message": "Hello! This is a reminder that you have an appointment with Dr. Smith tomorrow at 2pm.",
    "callback_url": "https://your-app.com/webhook/appointment",
    "choice": {
      "prompt": "Would you like to confirm or cancel this appointment?",
      "options": [
        {"value": "confirmed", "synonyms": ["yes", "yeah", "confirm", "keep"]},
        {"value": "cancelled", "synonyms": ["no", "nope", "cancel", "reschedule"]}
      ],
      "timeout_seconds": 20,
      "repeat_count": 2
    }
  }' | jq
```

**Webhook payload received:**

```json
{
  "call_id": "out-1732945860-1",
  "status": "completed",
  "extension": "5551234567",
  "duration_seconds": 45.2,
  "message_played": true,
  "choice_response": "confirmed",
  "choice_raw_text": "yes that works for me",
  "error": null
}
```

**Handle the webhook (Python):**

```python
from fastapi import FastAPI

app = FastAPI()

@app.post("/webhook/appointment")
async def handle_appointment_webhook(payload: dict):
    call_id = payload["call_id"]
    choice = payload.get("choice_response")
    
    if choice == "confirmed":
        # ✅ Mark appointment confirmed
        await confirm_appointment(call_id)
        print(f"📅 Appointment confirmed: {call_id}")
        
    elif choice == "cancelled":
        # ❌ Open slot for rebooking
        await cancel_appointment(call_id)
        print(f"📅 Appointment cancelled: {call_id}")
        
    else:
        # ⚠️ No response - follow up later
        await schedule_followup(call_id)
        print(f"📅 No response, scheduling followup: {call_id}")
    
    return {"status": "ok"}
```

---

## 🏠 Home Assistant Integration

Trigger a weather call when leaving home:

![Home Assistant automation](screenshots/home-assistant-automation.png)
<!-- TODO: Screenshot of Home Assistant automation UI -->

### `automations.yaml`

```yaml
automation:
  - id: weather_announcement_leaving
    alias: "🌤️ Weather announcement when leaving"
    trigger:
      - platform: state
        entity_id: person.john
        from: "home"
        to: "not_home"
    condition:
      - condition: time
        after: "06:00:00"
        before: "10:00:00"
    action:
      - service: rest_command.weather_call
```

### `configuration.yaml`

```yaml
rest_command:
  weather_call:
    url: "http://sip-agent:8080/tools/WEATHER/call"
    method: POST
    content_type: "application/json"
    payload: >
      {
        "extension": "5551234567",
        "prefix": "Good morning! Before you head out, here's the weather."
      }
```

**What happens:**

```
┌─────────────────────────────────────────────────────────────┐
│ 🏠 Home Assistant Event                                     │
├─────────────────────────────────────────────────────────────┤
│ 📍 John left home at 7:30 AM                               │
│ 🔄 Trigger: person.john state changed to "not_home"        │
│ ✅ Condition: Time is between 6:00 AM and 10:00 AM         │
│ ⚡ Action: Call rest_command.weather_call                   │
│ 📞 SIP Agent receives webhook                               │
│ 🌤️ Weather tool executed                                   │
│ 📞 Outbound call to 5551234567                             │
│ 🔊 "Good morning! Before you head out..."                  │
└─────────────────────────────────────────────────────────────┘
```

---

## 🚨 Monitoring Alerts

Alert on-call engineer when system goes down:

```bash
curl -X POST http://sip-agent:8080/call \
  -H "Content-Type: application/json" \
  -d '{
    "extension": "5551234567",
    "message": "Alert! The production database server is not responding. CPU usage was at 98% before it went offline. Please investigate immediately.",
    "callback_url": "https://monitoring.example.com/alert-acknowledged",
    "choice": {
      "prompt": "Say acknowledge to confirm you received this alert.",
      "options": [
        {"value": "acknowledged", "synonyms": ["acknowledge", "ack", "got it", "on it"]}
      ],
      "timeout_seconds": 30
    }
  }' | jq
```

### Alertmanager Integration

**`alertmanager.yml`:**

```yaml
receivers:
  - name: 'phone-alert'
    webhook_configs:
      - url: 'http://alert-bridge:8000/alert'
        send_resolved: true
```

**Alert bridge service (Python):**

```python
from fastapi import FastAPI
import httpx

app = FastAPI()

# 📞 On-call rotation
ONCALL_NUMBERS = {
    "primary": "5551234567",
    "secondary": "5559876543"
}

@app.post("/alert")
async def handle_alert(alert: dict):
    # 📋 Build alert message
    alert_name = alert['labels']['alertname']
    severity = alert['labels'].get('severity', 'warning')
    description = alert['annotations'].get('description', 'No description')
    
    message = f"Alert: {alert_name}. Severity: {severity}. {description}"
    
    # 📞 Call on-call engineer
    async with httpx.AsyncClient() as client:
        response = await client.post(
            "http://sip-agent:8080/call",
            json={
                "extension": ONCALL_NUMBERS["primary"],
                "message": message,
                "callback_url": "http://alert-bridge:8000/ack",
                "choice": {
                    "prompt": "Say acknowledge to confirm.",
                    "options": [{"value": "ack", "synonyms": ["acknowledge", "ack", "got it"]}],
                    "timeout_seconds": 30
                }
            }
        )
    
    return {"status": "calling", "call_id": response.json()["call_id"]}

@app.post("/ack")
async def handle_ack(payload: dict):
    if payload.get("choice_response") == "ack":
        print(f"✅ Alert acknowledged by on-call")
        # Update incident management system
    else:
        print(f"⚠️ No acknowledgment, escalating to secondary")
        # Call secondary on-call
```

---

## 💊 Medication Reminders

Set up recurring medication reminders:

```bash
# 🌅 Morning medication (8 AM)
curl -X POST http://sip-agent:8080/schedule \
  -H "Content-Type: application/json" \
  -d '{
    "extension": "5551234567",
    "message": "Good morning! This is your reminder to take your morning medication.",
    "at_time": "08:00",
    "timezone": "America/New_York",
    "recurring": "daily"
  }' | jq

# 🌙 Evening medication (8 PM)
curl -X POST http://sip-agent:8080/schedule \
  -H "Content-Type: application/json" \
  -d '{
    "extension": "5551234567",
    "message": "Good evening! This is your reminder to take your evening medication.",
    "at_time": "20:00",
    "timezone": "America/New_York",
    "recurring": "daily"
  }' | jq
```

**List scheduled reminders:**

```bash
curl http://sip-agent:8080/schedule | jq
```

**Output:**

```json
[
  {
    "schedule_id": "med-morning",
    "extension": "5551234567",
    "scheduled_for": "2025-12-01T08:00:00-05:00",
    "remaining_seconds": 14400,
    "message": "Good morning! This is your reminder...",
    "tool": null,
    "recurring": "daily",
    "status": "pending"
  },
  {
    "schedule_id": "med-evening",
    "extension": "5551234567",
    "scheduled_for": "2025-12-01T20:00:00-05:00",
    "remaining_seconds": 57600,
    "message": "Good evening! This is your reminder...",
    "tool": null,
    "recurring": "daily",
    "status": "pending"
  }
]
```

---

## ⏰ Cron-based Scheduling

Use system cron for flexible scheduling:

**`/etc/cron.d/weather-calls`:**

```bash
# ┌───────────── minute (0-59)
# │ ┌───────────── hour (0-23)
# │ │ ┌───────────── day of month (1-31)
# │ │ │ ┌───────────── month (1-12)
# │ │ │ │ ┌───────────── day of week (0-6, Sun=0)
# │ │ │ │ │
# │ │ │ │ │

# 🌅 Weekday mornings at 6:30am
30 6 * * 1-5 root curl -s -X POST http://sip-agent:8080/tools/WEATHER/call \
  -H "Content-Type: application/json" \
  -d '{"extension":"1001","prefix":"Good morning! Time to wake up."}' > /dev/null

# 🌅 Weekend mornings at 8am
0 8 * * 0,6 root curl -s -X POST http://sip-agent:8080/tools/WEATHER/call \
  -H "Content-Type: application/json" \
  -d '{"extension":"1001","prefix":"Good morning!"}' > /dev/null

# 🌙 Nightly summary at 9pm
0 21 * * * root curl -s -X POST http://sip-agent:8080/tools/WEATHER/call \
  -H "Content-Type: application/json" \
  -d '{"extension":"1001","prefix":"Here is your evening weather."}' > /dev/null
```

---

## 🔄 n8n Workflow Integration

Create a workflow that calls when a form is submitted:

![n8n workflow](screenshots/n8n-workflow.png)
<!-- TODO: Screenshot of n8n workflow -->

**Workflow JSON:**

```json
{
  "name": "Contact Form → Phone Call",
  "nodes": [
    {
      "name": "Webhook",
      "type": "n8n-nodes-base.webhook",
      "position": [250, 300],
      "parameters": {
        "path": "contact-form",
        "httpMethod": "POST"
      }
    },
    {
      "name": "HTTP Request",
      "type": "n8n-nodes-base.httpRequest",
      "position": [450, 300],
      "parameters": {
        "method": "POST",
        "url": "http://sip-agent:8080/call",
        "jsonParameters": true,
        "options": {},
        "bodyParametersJson": "={ \"message\": \"You have a new contact form submission from {{ $json.name }}. They said: {{ $json.message }}\", \"extension\": \"1001\" }"
      }
    }
  ],
  "connections": {
    "Webhook": {
      "main": [[{"node": "HTTP Request", "type": "main", "index": 0}]]
    }
  }
}
```

**Trigger the workflow:**

```bash
curl -X POST http://n8n:5678/webhook/contact-form \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Smith",
    "email": "john@example.com",
    "message": "I am interested in your services"
  }'
```

---

## 🐍 Python SDK

```python
import httpx
from typing import Optional


class SIPAssistant:
    """Python client for the SIP AI Assistant API."""
    
    def __init__(self, base_url: str = "http://sip-agent:8080"):
        self.base_url = base_url
    
    async def call(
        self,
        extension: str,
        message: str,
        callback_url: Optional[str] = None
    ) -> dict:
        """📞 Make an outbound call."""
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{self.base_url}/call",
                json={
                    "extension": extension,
                    "message": message,
                    "callback_url": callback_url
                }
            )
            return response.json()
    
    async def weather_call(
        self,
        extension: str,
        prefix: Optional[str] = None
    ) -> dict:
        """🌤️ Call with weather update."""
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{self.base_url}/tools/WEATHER/call",
                json={
                    "extension": extension,
                    "prefix": prefix
                }
            )
            return response.json()
    
    async def schedule_daily_weather(
        self,
        extension: str,
        time: str,
        timezone: str = "America/Los_Angeles"
    ) -> dict:
        """⏰ Schedule daily weather calls."""
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{self.base_url}/schedule",
                json={
                    "extension": extension,
                    "tool": "WEATHER",
                    "at_time": time,
                    "timezone": timezone,
                    "recurring": "daily",
                    "prefix": "Good morning! Here's your weather."
                }
            )
            return response.json()
    
    async def get_weather(self) -> dict:
        """🌡️ Get weather data without calling."""
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{self.base_url}/tools/WEATHER/execute",
                json={}
            )
            return response.json()


# 📖 Usage example
async def main():
    assistant = SIPAssistant()
    
    # 🌤️ Get weather
    weather = await assistant.get_weather()
    print(f"Weather: {weather['message']}")
    
    # ⏰ Schedule daily briefing
    result = await assistant.schedule_daily_weather(
        extension="5551234567",
        time="07:00"
    )
    print(f"Scheduled: {result['schedule_id']}")
    
    # 📞 Make a call
    call = await assistant.call(
        extension="5551234567",
        message="Hello! This is a test call."
    )
    print(f"Call ID: {call['call_id']}")


if __name__ == "__main__":
    import asyncio
    asyncio.run(main())
```

---

## 📊 Grafana Alert Integration

Configure Grafana to call via webhook:

![Grafana alerting](screenshots/grafana-alerting.png)
<!-- TODO: Screenshot of Grafana contact point configuration -->

**Contact Point (YAML):**

```yaml
apiVersion: 1
contactPoints:
  - name: phone-alert
    receivers:
      - uid: phone
        type: webhook
        settings:
          url: http://sip-agent:8080/call
          httpMethod: POST
        secureSettings: {}
```

**Notification Template:**

```go
{{ define "phone.message" }}
Alert: {{ .CommonLabels.alertname }}
Status: {{ .Status }}
{{ range .Alerts }}
{{ .Annotations.summary }}
{{ end }}
{{ end }}
```

---

## 🐳 Docker Health Check

Monitor the assistant and restart if unhealthy:

```yaml
services:
  sip-agent:
    image: sip-agent:latest
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s
    restart: unless-stopped
```

**Check health manually:**

```bash
docker inspect --format='{{.State.Health.Status}}' sip-agent
```

**Expected output:**

```
healthy
```