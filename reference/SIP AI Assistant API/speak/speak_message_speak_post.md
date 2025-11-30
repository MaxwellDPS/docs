---
title: Speak Message
excerpt: |-
  Speak a message to the active call.

  This is useful for external systems to inject announcements
  into an ongoing call.

  Query params:
  - message: The text to speak
  - call_id: Optional specific call ID (if multiple calls active)
api:
  file: openapi.json
  operationId: speak_message_speak_post
hidden: false
---