---
title: Schedule Call
excerpt: |-
  Schedule a call for a future time.

  You can schedule a call with either a static message or a tool that
  will be executed at call time (e.g., WEATHER for fresh data).

  Time can be specified as:
  - delay_seconds: Number of seconds from now
  - at_time: ISO datetime (2025-01-15T07:00:00) or HH:MM time (07:00)

  Examples:

  Wake-up weather call in 8 hours:
  ```json
  {
      "extension": "1001",
      "tool": "WEATHER",
      "delay_seconds": 28800,
      "prefix": "Good morning! Here's your weather."
  }
  ```

  Daily 7am weather call:
  ```json
  {
      "extension": "1001",
      "tool": "WEATHER",
      "at_time": "07:00",
      "timezone": "America/Los_Angeles",
      "prefix": "Good morning!",
      "recurring": "daily"
  }
  ```

  Reminder call at specific time:
  ```json
  {
      "extension": "5551234567",
      "message": "This is your reminder to take your medication.",
      "at_time": "2025-01-15T09:00:00",
      "timezone": "America/New_York"
  }
  ```

  Weekday morning briefing:
  ```json
  {
      "extension": "1001",
      "tool": "WEATHER",
      "at_time": "06:30",
      "recurring": "weekdays",
      "prefix": "Good morning! Time to wake up."
  }
  ```
api:
  file: openapi.json
  operationId: schedule_call_schedule_post
hidden: false
---