---
title: Initiate Call
excerpt: >-
  Initiate an outbound notification call.


  The call will be made asynchronously. If callback_url is provided,

  results will be POSTed there when the call completes.


  Calls are queued and processed sequentially to prevent overwhelming the SIP
  system.


  Note: callback_url is required when using choice collection.


  Simple notification example:

  ```json

  {
      "message": "Hello, this is a reminder about your appointment.",
      "extension": "1001"
  }

  ```


  Choice collection example (requires callback_url):

  ```json

  {
      "message": "Hello, this is a reminder about your appointment tomorrow at 2pm.",
      "extension": "1001",
      "callback_url": "https://example.com/webhook",
      "choice": {
          "prompt": "Say yes to confirm or no to cancel.",
          "options": [
              {"value": "confirmed", "synonyms": ["yes", "yeah", "yep", "confirm"]},
              {"value": "cancelled", "synonyms": ["no", "nope", "cancel"]}
          ],
          "timeout_seconds": 15
      }
  }

  ```
api:
  file: openapi.json
  operationId: initiate_call_call_post
hidden: false
---