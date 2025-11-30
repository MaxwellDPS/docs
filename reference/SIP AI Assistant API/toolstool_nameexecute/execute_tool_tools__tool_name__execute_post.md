---
title: Execute Tool
excerpt: |-
  Execute a tool and optionally speak the result.

  This endpoint allows external systems (webhooks, home automation, etc.)
  to trigger tool execution. The result can optionally be spoken to 
  an active call.

  Examples:

  Get weather (just data):
  ```json
  POST /tools/WEATHER/execute
  {"tool": "WEATHER"}
  ```

  Get weather and speak to call:
  ```json
  POST /tools/WEATHER/execute
  {"tool": "WEATHER", "speak_result": true}
  ```

  Execute calculation:
  ```json
  POST /tools/CALC/execute
  {"tool": "CALC", "params": {"expression": "25 * 4"}}
  ```

  Set a timer and announce it:
  ```json
  POST /tools/SET_TIMER/execute
  {
      "tool": "SET_TIMER",
      "params": {"duration": 300, "message": "Pizza is ready!"},
      "speak_result": true
  }
  ```
api:
  file: openapi.json
  operationId: execute_tool_tools__tool_name__execute_post
hidden: false
---