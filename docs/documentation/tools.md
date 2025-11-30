---
title: "Built-in Tools"
excerpt: "Available tools for the voice assistant"
category:
  uri: features
slug: tools
---

> 🤖 **ROBO CODED** — This documentation was made with AI and may not be 100% sane. But the code does work! 🎉

# 🔧 Built-in Tools

The SIP AI Assistant includes several built-in tools that the LLM can invoke during conversations.

![Tools demo](screenshots/tools-demo.png)
<!-- TODO: Screenshot of log viewer showing tool execution -->

---

## 🎯 How Tools Work

```
┌─────────────────────────────────────────────────────────────┐
│ 👤 User: "Set a timer for 5 minutes"                        │
├─────────────────────────────────────────────────────────────┤
│ 1. 🎤 Speech → Text (Whisper)                               │
│ 2. 🧠 LLM recognizes timer request                          │
│ 3. 🔧 LLM outputs: [TOOL:SET_TIMER:duration=300]           │
│ 4. ⚙️ Tool executes, returns result                        │
│ 5. 🔊 Assistant speaks: "Timer set for 5 minutes!"         │
└─────────────────────────────────────────────────────────────┘
```

**Tool invocation format:**

```
[TOOL:NAME]
[TOOL:NAME:param1=value1,param2=value2]
```

---

## 🌤️ WEATHER

Get current weather conditions from a Tempest weather station.

| Property | Value |
|----------|-------|
| **Name** | `WEATHER` |
| **Parameters** | None |
| **Requires** | `TEMPEST_STATION_ID`, `TEMPEST_API_TOKEN` |

### 🗣️ Trigger Phrases

- *"What's the weather?"*
- *"How's the weather outside?"*
- *"What's the temperature?"*
- *"Is it raining?"*

### 📤 Example Output

```
🤖 "At Storm Lake, as of 9:30 pm, it's 44 degrees with foggy 
    conditions. Wind is calm. Yesterday saw half an inch of rain."
```

### ✨ Features

- 🌡️ Temperature with feels-like
- 💧 Humidity and fog detection
- 💨 Wind speed, direction, gusts
- 🌧️ Precipitation (current, today, yesterday)
- ⚡ Lightning detection with distance
- ☀️ UV index warnings
- 📊 Barometric pressure trends

### 🔧 Configuration

```env
TEMPEST_STATION_ID=12345
TEMPEST_API_TOKEN=your-api-token
```

---

## ⏲️ SET_TIMER

Set a timer that fires during or after the call.

| Property | Value |
|----------|-------|
| **Name** | `SET_TIMER` |
| **Parameters** | `duration` (required), `message` (optional) |

### 📋 Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|:--------:|---------|-------------|
| `duration` | integer | ✅ | - | Duration in seconds |
| `message` | string | ❌ | `"Your timer is complete"` | Completion message |

### 🗣️ Trigger Phrases

- *"Set a timer for 5 minutes"*
- *"Remind me in 30 seconds"*
- *"Set a 2 hour timer for the roast"*

### 📤 Example Conversation

```
👤 "Set a timer for 10 minutes for my pizza"
🤖 "Timer set for 10 minutes!"

   ⏳ ... 10 minutes later ...

🤖 "Your pizza timer is complete!"
```

### ⚙️ Tool Invocation

```
[TOOL:SET_TIMER:duration=600,message=Pizza is ready!]
```

### ⚠️ Limits

- Maximum duration: 24 hours (configurable via `MAX_TIMER_DURATION_HOURS`)

---

## 📞 CALLBACK

Schedule a callback - the assistant will call you back later.

| Property | Value |
|----------|-------|
| **Name** | `CALLBACK` |
| **Parameters** | `delay`, `message`, `destination` |

### 📋 Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|:--------:|---------|-------------|
| `delay` | integer | ❌ | `60` | Delay in seconds |
| `message` | string | ❌ | `"This is your callback"` | Message to speak |
| `destination` | string | ❌ | Current caller | Phone to call |

### 🗣️ Trigger Phrases

- *"Call me back in 30 minutes"*
- *"Remind me to check the oven in 20 minutes"*
- *"Schedule a callback for 5pm"*

### 📤 Example Conversation

```
👤 "Call me back in an hour to remind me about my meeting"
🤖 "I'll call you back in 1 hour!"

   ⏳ ... 1 hour later ...
   
📞 Phone rings...
🤖 "This is your reminder about your meeting!"
```

---

## 📴 HANGUP

End the current call gracefully.

| Property | Value |
|----------|-------|
| **Name** | `HANGUP` |
| **Parameters** | None |

### 🗣️ Trigger Phrases

- *"Goodbye"*
- *"Hang up"*
- *"End the call"*
- *"That's all, thanks"*

### 📤 Example

```
👤 "Thanks, that's all I needed. Goodbye!"
🤖 "You're welcome! Have a great day! Goodbye!"
📴 Call ended
```

---

## 📋 STATUS

Check status of pending timers and scheduled callbacks.

| Property | Value |
|----------|-------|
| **Name** | `STATUS` |
| **Parameters** | None |

### 🗣️ Trigger Phrases

- *"What timers do I have?"*
- *"Check my callbacks"*
- *"What's scheduled?"*
- *"Any pending reminders?"*

### 📤 Example Output

```
🤖 "You have 1 timer: 4 minutes remaining for your pizza. 
    You also have a callback scheduled in 45 minutes."
```

---

## ❌ CANCEL

Cancel pending timers or callbacks.

| Property | Value |
|----------|-------|
| **Name** | `CANCEL` |
| **Parameters** | `task_type` |

### 📋 Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|:--------:|---------|-------------|
| `task_type` | string | ❌ | `"all"` | `"timer"`, `"callback"`, or `"all"` |

### 🗣️ Trigger Phrases

- *"Cancel my timer"*
- *"Cancel all timers"*
- *"Cancel my callback"*
- *"Cancel everything"*

### 📤 Example

```
👤 "Cancel my timer"
🤖 "Timer cancelled!"
```

---

## 🕐 DATETIME

Get the current date and/or time.

| Property | Value |
|----------|-------|
| **Name** | `DATETIME` |
| **Parameters** | `format`, `timezone` |

### 📋 Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|:--------:|---------|-------------|
| `format` | string | ❌ | `"datetime"` | Output format |
| `timezone` | string | ❌ | System TZ | Timezone name |

### 🎯 Format Options

| Format | Example Output |
|--------|----------------|
| `time` | *"It's 3:45 PM"* |
| `date` | *"Today is Saturday, November 30th, 2025"* |
| `datetime` | *"It's Saturday, November 30th at 3:45 PM"* |
| `full` | *"It's Saturday, November 30th, 2025 at 3:45:30 PM Pacific Standard Time"* |

### 🗣️ Trigger Phrases

- *"What time is it?"*
- *"What's today's date?"*
- *"What day is it?"*

---

## 🧮 CALC

Perform mathematical calculations.

| Property | Value |
|----------|-------|
| **Name** | `CALC` |
| **Parameters** | `expression` (required) |

### 📋 Parameters

| Parameter | Type | Required | Description |
|-----------|------|:--------:|-------------|
| `expression` | string | ✅ | Math expression |

### ➕ Supported Operations

| Operator | Description | Example |
|----------|-------------|---------|
| `+` | Addition | `5 + 3` |
| `-` | Subtraction | `10 - 4` |
| `*` | Multiplication | `6 * 7` |
| `/` | Division | `15 / 3` |
| `//` | Integer division | `17 // 5` |
| `%` | Modulo | `10 % 3` |
| `**` | Exponentiation | `2 ** 8` |
| `()` | Parentheses | `(5 + 3) * 2` |

### 🗣️ Trigger Phrases

- *"What's 25 times 4?"*
- *"Calculate 15% of 200"*
- *"What's 144 divided by 12?"*

### 📤 Example

```
👤 "What's 25 percent of 80?"
🤖 "25 percent of 80 is 20"
```

### 🔒 Safety

- Uses AST parsing (not `eval()`)
- Only allows numeric operations
- No code execution possible

---

## 😄 JOKE

Tell a random joke.

| Property | Value |
|----------|-------|
| **Name** | `JOKE` |
| **Parameters** | `category` |

### 📋 Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|:--------:|---------|-------------|
| `category` | string | ❌ | `"general"` | Joke category |

### 🎭 Categories

| Category | Description |
|----------|-------------|
| `general` | General humor |
| `tech` | Tech/programming jokes |
| `dad` | Classic dad jokes |

### 🗣️ Trigger Phrases

- *"Tell me a joke"*
- *"Got any dad jokes?"*
- *"Tell me a tech joke"*

---

## ⚙️ Tool Invocation Reference

The LLM invokes tools using this format:

```
[TOOL:TOOL_NAME]
[TOOL:TOOL_NAME:param1=value1,param2=value2]
```

### 📝 Examples

```
[TOOL:WEATHER]
[TOOL:SET_TIMER:duration=300,message=Pizza is ready]
[TOOL:CALLBACK:delay=3600,message=Meeting reminder]
[TOOL:CALC:expression=25*4]
[TOOL:DATETIME:format=full,timezone=America/New_York]
[TOOL:CANCEL:task_type=timer]
[TOOL:JOKE:category=dad]
```

---

## 🔌 Creating Custom Tools

Want to add your own tools? See [Creating Plugins](plugins).

```python
# Example: Hello World tool
class HelloTool(BaseTool):
    name = "HELLO"
    description = "Say hello"
    
    async def execute(self, params):
        return ToolResult(
            status=ToolStatus.SUCCESS,
            message="Hello, world!"
        )
```