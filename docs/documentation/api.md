---
title: "API Reference"
excerpt: "REST API endpoints for the SIP AI Assistant"
category:
  uri: api
slug: api-reference
---

> 🤖 **ROBO CODED** — This documentation was made with AI and may not be 100% sane. But the code does work! 🎉

# 🌐 API Reference

The SIP AI Assistant exposes a REST API for initiating calls, executing tools, and managing scheduled tasks.

**Base URL:** `http://your-server:8080`

> 💡 **Interactive Docs:** Visit `http://your-server:8080/docs` for Swagger UI

![Swagger UI](screenshots/swagger-ui.png)
<!-- TODO: Screenshot of FastAPI Swagger docs -->

---

## 🏥 Health & Status

### `GET /health`

Check service health and SIP registration status.

```bash
curl http://localhost:8080/health | jq
```

**Response:**

```json
{
  "status": "healthy",
  "sip_registered": true,
  "active_calls": 0
}
```

---

### `GET /queue`

Get call queue status.

```bash
curl http://localhost:8080/queue | jq
```

**Response:**

```json
{
  "enabled": true,
  "pending": 2,
  "active": 1,
  "max_concurrent": 1,
  "total_processed": 47
}
```

---

## 📞 Outbound Calls

### `POST /call`

Initiate an outbound notification call.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `message` | string | Yes | Message to speak |
| `extension` | string | Yes | Phone number or SIP extension |
| `callback_url` | string | No | Webhook for results |
| `ring_timeout` | integer | No | Seconds to wait (default: 30) |
| `call_id` | string | No | Custom call ID |
| `choice` | object | No | Choice collection config |

#### 📤 Simple Notification

```bash
curl -X POST http://localhost:8080/call \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Hello! This is a reminder about your appointment tomorrow at 2pm.",
    "extension": "5551234567"
  }' | jq
```

**Response:**

```json
{
  "call_id": "out-1732945860-1",
  "status": "queued",
  "message": "Call initiated",
  "queue_position": null
}
```

#### 📤 With Choice Collection

```bash
curl -X POST http://localhost:8080/call \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Hello! Your appointment is tomorrow at 2pm.",
    "extension": "5551234567",
    "callback_url": "https://example.com/webhook",
    "choice": {
      "prompt": "Say yes to confirm or no to cancel.",
      "options": [
        {"value": "confirmed", "synonyms": ["yes", "yeah", "confirm"]},
        {"value": "cancelled", "synonyms": ["no", "nope", "cancel"]}
      ],
      "timeout_seconds": 15
    }
  }' | jq
```

---

### `GET /call/{call_id}`

Get status of a specific call.

```bash
curl http://localhost:8080/call/out-1732945860-1 | jq
```

**Response:**

```json
{
  "call_id": "out-1732945860-1",
  "status": "completed",
  "queued_at": "2025-11-30T10:30:00Z",
  "started_at": "2025-11-30T10:30:05Z",
  "completed_at": "2025-11-30T10:30:45Z",
  "duration_seconds": 40,
  "choice_response": "confirmed",
  "error": null
}
```

---

## 🔧 Tools

### `GET /tools`

List all available tools.

```bash
curl http://localhost:8080/tools | jq
```

**Response:**

```json
[
  {
    "name": "WEATHER",
    "description": "Get current weather conditions",
    "parameters": {},
    "enabled": true
  },
  {
    "name": "SET_TIMER",
    "description": "Set a timer for a specified duration",
    "parameters": {
      "duration": {"type": "integer", "required": true},
      "message": {"type": "string", "required": false}
    },
    "enabled": true
  },
  {
    "name": "DATETIME",
    "description": "Get current date and time",
    "parameters": {
      "format": {"type": "string", "required": false},
      "timezone": {"type": "string", "required": false}
    },
    "enabled": true
  }
]
```

---

### `GET /tools/{tool_name}`

Get details about a specific tool.

```bash
curl http://localhost:8080/tools/WEATHER | jq
```

**Response:**

```json
{
  "name": "WEATHER",
  "description": "Get current weather conditions from the local weather station",
  "parameters": {},
  "enabled": true
}
```

---

### `POST /tools/{tool_name}/execute`

Execute a tool and get the result.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `params` | object | No | Tool parameters |
| `speak_result` | boolean | No | Speak to active call |
| `call_id` | string | No | Specific call ID |

#### 🌤️ Execute Weather Tool

```bash
curl -X POST http://localhost:8080/tools/WEATHER/execute \
  -H "Content-Type: application/json" \
  -d '{}' | jq
```

**Response:**

```json
{
  "success": true,
  "tool": "WEATHER",
  "message": "At Storm Lake, as of 9:30 pm, it's 44 degrees with foggy conditions. Wind is calm.",
  "data": {
    "temp_f": 44,
    "feels_like_f": 44,
    "humidity": 98,
    "wind_mph": 0,
    "conditions": "foggy"
  },
  "spoken": false,
  "error": null
}
```

#### 🧮 Execute Calculator

```bash
curl -X POST http://localhost:8080/tools/CALC/execute \
  -H "Content-Type: application/json" \
  -d '{"params": {"expression": "25 * 4 + 10"}}' | jq
```

**Response:**

```json
{
  "success": true,
  "tool": "CALC",
  "message": "The result is 110",
  "data": {"result": 110},
  "spoken": false,
  "error": null
}
```

#### 🕐 Execute DateTime

```bash
curl -X POST http://localhost:8080/tools/DATETIME/execute \
  -H "Content-Type: application/json" \
  -d '{"params": {"format": "full", "timezone": "America/New_York"}}' | jq
```

**Response:**

```json
{
  "success": true,
  "tool": "DATETIME",
  "message": "It's Saturday, November 30th, 2025 at 6:30:00 PM Eastern Standard Time",
  "data": {
    "datetime": "2025-11-30T18:30:00-05:00",
    "timezone": "America/New_York"
  },
  "spoken": false,
  "error": null
}
```

---

### `POST /tools/{tool_name}/call`

Execute a tool and call someone with the result.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `extension` | string | Yes | Phone number to call |
| `params` | object | No | Tool parameters |
| `prefix` | string | No | Message before tool result |
| `suffix` | string | No | Message after tool result |
| `ring_timeout` | integer | No | Ring timeout in seconds |
| `callback_url` | string | No | Webhook for results |

#### 🌤️ Weather Call

```bash
curl -X POST http://localhost:8080/tools/WEATHER/call \
  -H "Content-Type: application/json" \
  -d '{
    "extension": "5551234567",
    "prefix": "Good morning! Here is your weather update.",
    "suffix": "Have a great day!"
  }' | jq
```

**Response:**

```json
{
  "call_id": "out-1732945860-2",
  "status": "initiated",
  "tool": "WEATHER",
  "tool_success": true,
  "tool_message": "At Storm Lake, as of 7:00 am, it's 38 degrees...",
  "message": "Calling 5551234567 with WEATHER result"
}
```

**Call flow:**

```
┌─────────────────────────────────────────────────────────────┐
│ 📤 POST /tools/WEATHER/call                                 │
├─────────────────────────────────────────────────────────────┤
│ 1. 🌤️ Execute WEATHER tool                                 │
│ 2. 📞 Dial 5551234567                                       │
│ 3. 🔔 Ring... ring... ring...                              │
│ 4. 📱 Call answered                                         │
│ 5. 🔊 "Good morning! Here is your weather update."         │
│ 6. 🔊 "At Storm Lake, it's 38 degrees..."                  │
│ 7. 🔊 "Have a great day!"                                  │
│ 8. 📴 Hangup                                               │
└─────────────────────────────────────────────────────────────┘
```

---

## ⏰ Scheduled Calls

### `POST /schedule`

Schedule a call for a future time.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `extension` | string | Yes | Phone number to call |
| `message` | string | ⚠️ | Static message (or use `tool`) |
| `tool` | string | ⚠️ | Tool to execute at call time |
| `tool_params` | object | No | Parameters for the tool |
| `delay_seconds` | integer | ⚠️ | Seconds from now |
| `at_time` | string | ⚠️ | ISO datetime or HH:MM |
| `timezone` | string | No | Timezone (default: America/Los_Angeles) |
| `prefix` | string | No | Message before tool result |
| `suffix` | string | No | Message after tool result |
| `recurring` | string | No | `daily`, `weekdays`, `weekends` |
| `callback_url` | string | No | Webhook for results |

> ⚠️ Either `message` or `tool` required. Either `delay_seconds` or `at_time` required.

#### ⏰ Schedule Weather in 8 Hours

```bash
curl -X POST http://localhost:8080/schedule \
  -H "Content-Type: application/json" \
  -d '{
    "extension": "1001",
    "tool": "WEATHER",
    "delay_seconds": 28800,
    "prefix": "Good morning! Here is your weather."
  }' | jq
```

**Response:**

```json
{
  "schedule_id": "a1b2c3d4",
  "status": "scheduled",
  "extension": "1001",
  "scheduled_for": "2025-12-01T07:00:00-08:00",
  "delay_seconds": 28800,
  "message": "Call scheduled for 2025-12-01T07:00:00-08:00",
  "recurring": null
}
```

#### 🌅 Daily 7am Weather Call

```bash
curl -X POST http://localhost:8080/schedule \
  -H "Content-Type: application/json" \
  -d '{
    "extension": "1001",
    "tool": "WEATHER",
    "at_time": "07:00",
    "timezone": "America/Los_Angeles",
    "recurring": "daily",
    "prefix": "Good morning!"
  }' | jq
```

**Response:**

```json
{
  "schedule_id": "b2c3d4e5",
  "status": "scheduled",
  "extension": "1001",
  "scheduled_for": "2025-12-01T07:00:00-08:00",
  "delay_seconds": 36000,
  "message": "Call scheduled for 2025-12-01T07:00:00-08:00",
  "recurring": "daily"
}
```

#### 💊 One-time Medication Reminder

```bash
curl -X POST http://localhost:8080/schedule \
  -H "Content-Type: application/json" \
  -d '{
    "extension": "5551234567",
    "message": "This is your reminder to take your medication.",
    "at_time": "2025-12-01T09:00:00",
    "timezone": "America/New_York"
  }' | jq
```

---

### `GET /schedule`

List all scheduled calls.

```bash
curl http://localhost:8080/schedule | jq
```

**Response:**

```json
[
  {
    "schedule_id": "a1b2c3d4",
    "extension": "1001",
    "scheduled_for": "2025-12-01T07:00:00-08:00",
    "remaining_seconds": 25200,
    "message": null,
    "tool": "WEATHER",
    "recurring": "daily",
    "status": "pending"
  },
  {
    "schedule_id": "b2c3d4e5",
    "extension": "5551234567",
    "scheduled_for": "2025-12-01T09:00:00-05:00",
    "remaining_seconds": 32400,
    "message": "Take your medication",
    "tool": null,
    "recurring": null,
    "status": "pending"
  }
]
```

---

### `GET /schedule/{schedule_id}`

Get details of a scheduled call.

```bash
curl http://localhost:8080/schedule/a1b2c3d4 | jq
```

---

### `DELETE /schedule/{schedule_id}`

Cancel a scheduled call.

```bash
curl -X DELETE http://localhost:8080/schedule/a1b2c3d4 | jq
```

**Response:**

```json
{
  "success": true,
  "message": "Scheduled call a1b2c3d4 cancelled"
}
```

---

## 🔊 Speak to Active Call

### `POST /speak`

Inject a message into an active call.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|:--------:|-------------|
| `message` | string | Yes | Text to speak |
| `call_id` | string | No | Specific call ID |

```bash
curl -X POST "http://localhost:8080/speak?message=Attention:%20severe%20weather%20warning" | jq
```

**Response:**

```json
{
  "success": true,
  "message": "Message spoken to call"
}
```

---

## 🔗 Webhooks

When you provide a `callback_url`, results are POSTed as JSON.

### 📞 Outbound Call Webhook

```json
{
  "call_id": "out-1732945860-1",
  "status": "completed",
  "extension": "5551234567",
  "duration_seconds": 45.2,
  "message_played": true,
  "choice_response": "confirmed",
  "choice_raw_text": "yes that works",
  "error": null
}
```

### ⏰ Scheduled Call Webhook

```json
{
  "schedule_id": "a1b2c3d4",
  "status": "completed",
  "extension": "1001",
  "tool": "WEATHER",
  "recurring": "daily",
  "timestamp": "2025-12-01T07:00:45Z"
}
```

---

## ❌ Error Responses

All errors return appropriate HTTP status codes:

| Status | Description |
|--------|-------------|
| `400` | Bad request (invalid parameters) |
| `404` | Resource not found |
| `500` | Internal server error |

**Example error:**

```json
{
  "detail": "Tool 'INVALID' not found. Use GET /tools to list available tools."
}
```

---

## 📊 Quick Reference

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | 🏥 Health check |
| `/queue` | GET | 📊 Queue status |
| `/call` | POST | 📞 Initiate outbound call |
| `/call/{id}` | GET | 📋 Get call status |
| `/tools` | GET | 🔧 List all tools |
| `/tools/{name}` | GET | 🔧 Get tool info |
| `/tools/{name}/execute` | POST | ▶️ Execute tool |
| `/tools/{name}/call` | POST | 📞 Execute tool + call |
| `/schedule` | POST | ⏰ Schedule a call |
| `/schedule` | GET | 📋 List scheduled calls |
| `/schedule/{id}` | GET | 📋 Get scheduled call |
| `/schedule/{id}` | DELETE | ❌ Cancel scheduled call |
| `/speak` | POST | 🔊 Speak to active call |
