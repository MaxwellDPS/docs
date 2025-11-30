---
title: Tool Call
excerpt: |-
  Execute a tool and call someone with the result.

  This endpoint is perfect for webhooks - it executes a tool (like WEATHER),
  then places an outbound call to speak the result to the recipient.

  Examples:

  Weather alert call:
  ```json
  POST /tools/WEATHER/call
  {
      "tool": "WEATHER",
      "extension": "1001",
      "prefix": "Good morning! Here's your weather update.",
      "suffix": "Have a great day!"
  }
  ```

  Scheduled weather call (from cron/Home Assistant):
  ```bash
  curl -X POST http://sip-agent:8080/tools/WEATHER/call \
    -H "Content-Type: application/json" \
    -d '{"tool": "WEATHER", "extension": "5551234567"}'
  ```

  DateTime announcement:
  ```json
  POST /tools/DATETIME/call
  {
      "tool": "DATETIME",
      "params": {"format": "full"},
      "extension": "1001",
      "prefix": "Attention please."
  }
  ```

  With callback for confirmation:
  ```json
  {
      "tool": "WEATHER",
      "extension": "1001",
      "callback_url": "https://example.com/webhook/weather-call-complete"
  }
  ```
api:
  file: openapi.json
  operationId: tool_call_tools__tool_name__call_post
hidden: false
---