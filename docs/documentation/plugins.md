---
title: "Creating Plugins"
excerpt: "Build custom tools for the voice assistant"
category:
  uri: development
slug: plugins
---

> 🤖 **ROBO CODED** — This documentation was made with AI and may not be 100% sane. But the code does work! 🎉

# 🔌 Creating Plugins

Extend the SIP AI Assistant with custom tools by creating Python plugins.

![Plugin code](screenshots/plugin-code.png)
<!-- TODO: Screenshot of VS Code with a plugin file open -->

---

## 🎯 Plugin Basics

Plugins are Python files in `src/plugins/` that define tool classes:

```
src/plugins/
├── __init__.py
├── weather_tool.py
├── timer_tool.py
├── callback_tool.py
└── my_custom_tool.py   # 👈 Your plugin here!
```

Each plugin:
1. 📦 Inherits from `BaseTool`
2. 📝 Defines name, description, and parameters
3. ⚡ Implements an `execute()` method

---

## 🚀 Quick Start

### Step 1: Create the Plugin File

Create `src/plugins/hello_tool.py`:

```python
"""
Hello Tool
==========
A simple greeting tool.
"""

from typing import Any, Dict
from tool_plugins import BaseTool, ToolResult, ToolStatus


class HelloTool(BaseTool):
    # 🏷️ Unique tool name (UPPERCASE)
    name = "HELLO"
    
    # 📝 Description shown to LLM
    description = "Say hello to someone"
    
    # ✅ Enable/disable
    enabled = True
    
    # 📋 Parameter definitions
    parameters = {
        "name": {
            "type": "string",
            "description": "Name to greet",
            "required": True
        }
    }
    
    async def execute(self, params: Dict[str, Any]) -> ToolResult:
        name = params.get("name", "friend")
        
        return ToolResult(
            status=ToolStatus.SUCCESS,
            message=f"Hello, {name}! Nice to meet you."
        )
```

### Step 2: Register the Plugin

Edit `src/tool_manager.py`:

```python
def _load_tools(self):
    """Load all tool plugins."""
    # Add import at top of method
    from plugins.hello_tool import HelloTool
    
    tool_classes = [
        # ... existing tools ...
        HelloTool,  # 👈 Add your tool here
    ]
```

### Step 3: Restart and Test

```bash
# Restart the service
docker compose restart sip-agent

# Verify tool is loaded
curl http://localhost:8080/tools | jq '.[].name'
```

**Expected output:**

```
"WEATHER"
"SET_TIMER"
"CALLBACK"
...
"HELLO"    # 👈 Your new tool!
```

### Step 4: Test Execution

```bash
curl -X POST http://localhost:8080/tools/HELLO/execute \
  -H "Content-Type: application/json" \
  -d '{"params": {"name": "World"}}' | jq
```

**Expected output:**

```json
{
  "success": true,
  "tool": "HELLO",
  "message": "Hello, World! Nice to meet you.",
  "data": null,
  "spoken": false,
  "error": null
}
```

![Plugin test](screenshots/plugin-test.png)
<!-- TODO: Screenshot of terminal showing plugin test -->

---

## 📋 Tool Class Structure

```python
from typing import Any, Dict
from tool_plugins import BaseTool, ToolResult, ToolStatus


class MyTool(BaseTool):
    # ═══════════════════════════════════════════════════════════
    # 🏷️ REQUIRED: Unique tool name (UPPERCASE)
    # ═══════════════════════════════════════════════════════════
    name = "MY_TOOL"
    
    # ═══════════════════════════════════════════════════════════
    # 📝 REQUIRED: Description (shown to LLM)
    # ═══════════════════════════════════════════════════════════
    description = "What this tool does"
    
    # ═══════════════════════════════════════════════════════════
    # ✅ OPTIONAL: Enable/disable (default: True)
    # ═══════════════════════════════════════════════════════════
    enabled = True
    
    # ═══════════════════════════════════════════════════════════
    # 📋 OPTIONAL: Parameter definitions
    # ═══════════════════════════════════════════════════════════
    parameters = {
        "param_name": {
            "type": "string",      # string, integer, number, boolean
            "description": "...",   # Help text for LLM
            "required": True,       # Required or optional
            "default": "value"      # Default if not provided
        }
    }
    
    # ═══════════════════════════════════════════════════════════
    # 🔧 OPTIONAL: Custom initialization
    # ═══════════════════════════════════════════════════════════
    def __init__(self, assistant):
        super().__init__(assistant)
        # Access config: self.config
        # Access assistant: self.assistant
    
    # ═══════════════════════════════════════════════════════════
    # ⚡ REQUIRED: Execute the tool
    # ═══════════════════════════════════════════════════════════
    async def execute(self, params: Dict[str, Any]) -> ToolResult:
        # Your tool logic here
        return ToolResult(
            status=ToolStatus.SUCCESS,
            message="Result to speak to user",
            data={"optional": "structured data"}
        )
```

---

## 📊 Parameter Types

| Type | Python Type | Example Value |
|------|-------------|---------------|
| `string` | `str` | `"hello"` |
| `integer` | `int` | `42` |
| `number` | `float` | `3.14` |
| `boolean` | `bool` | `true` / `false` |

Parameters are automatically validated and converted.

---

## 📤 Return Values

Always return a `ToolResult`:

```python
from tool_plugins import ToolResult, ToolStatus

# ✅ Success
return ToolResult(
    status=ToolStatus.SUCCESS,
    message="Message to speak to user",
    data={"key": "value"}  # Optional structured data
)

# ❌ Failure
return ToolResult(
    status=ToolStatus.FAILED,
    message="Error message to speak"
)

# ⏳ Pending (for async operations)
return ToolResult(
    status=ToolStatus.PENDING,
    message="Working on it...",
    data={"task_id": "abc123"}
)
```

---

## 🔗 Accessing Resources

Your tool has access to:

```python
class MyTool(BaseTool):
    async def execute(self, params):
        # ═══════════════════════════════════════════════════════
        # 📋 Configuration
        # ═══════════════════════════════════════════════════════
        api_key = self.config.my_api_key
        base_url = self.config.my_base_url
        
        # ═══════════════════════════════════════════════════════
        # 📞 Current call info
        # ═══════════════════════════════════════════════════════
        if self.assistant.current_call:
            caller_id = self.assistant.current_call.caller
            call_duration = self.assistant.current_call.duration
        
        # ═══════════════════════════════════════════════════════
        # ⏰ Schedule tasks
        # ═══════════════════════════════════════════════════════
        task_id = await self.assistant.tool_manager.schedule_task(
            task_type="my_task",
            delay_seconds=60,
            message="Task complete"
        )
        
        # ═══════════════════════════════════════════════════════
        # 🌐 HTTP requests
        # ═══════════════════════════════════════════════════════
        import httpx
        async with httpx.AsyncClient() as client:
            response = await client.get("https://api.example.com")
            data = response.json()
```

---

## 📖 Full Example: Stock Price Tool

```python
"""
Stock Price Tool
================
Get current stock prices from an API.
"""

import logging
import httpx
from typing import Any, Dict

from tool_plugins import BaseTool, ToolResult, ToolStatus

logger = logging.getLogger(__name__)


class StockPriceTool(BaseTool):
    name = "STOCK"
    description = "Get the current price of a stock"
    enabled = True
    
    parameters = {
        "symbol": {
            "type": "string",
            "description": "Stock ticker symbol (e.g., AAPL, GOOGL)",
            "required": True
        }
    }
    
    def __init__(self, assistant):
        super().__init__(assistant)
        # 🔧 Check for required config
        self.api_key = getattr(self.config, 'stock_api_key', None)
        if not self.api_key:
            self.enabled = False
            logger.info("📉 Stock tool disabled - no API key configured")
    
    async def execute(self, params: Dict[str, Any]) -> ToolResult:
        symbol = params.get("symbol", "").upper()
        
        # ✅ Validate input
        if not symbol:
            return ToolResult(
                status=ToolStatus.FAILED,
                message="Please specify a stock symbol"
            )
        
        try:
            # 🌐 Fetch stock data
            async with httpx.AsyncClient(timeout=10.0) as client:
                response = await client.get(
                    f"https://api.stockprovider.com/quote/{symbol}",
                    headers={"Authorization": f"Bearer {self.api_key}"}
                )
                
                if response.status_code == 404:
                    return ToolResult(
                        status=ToolStatus.FAILED,
                        message=f"Stock symbol {symbol} not found"
                    )
                
                response.raise_for_status()
                data = response.json()
            
            # 📊 Format result
            price = data.get("price", 0)
            change = data.get("change", 0)
            change_pct = data.get("change_percent", 0)
            
            direction = "up" if change >= 0 else "down"
            
            return ToolResult(
                status=ToolStatus.SUCCESS,
                message=f"{symbol} is at ${price:.2f}, {direction} {abs(change_pct):.1f}% today",
                data={
                    "symbol": symbol,
                    "price": price,
                    "change": change,
                    "change_percent": change_pct
                }
            )
            
        except httpx.TimeoutException:
            return ToolResult(
                status=ToolStatus.FAILED,
                message="Stock service is not responding"
            )
        except Exception as e:
            logger.error(f"Stock fetch error: {e}")
            return ToolResult(
                status=ToolStatus.FAILED,
                message="Error fetching stock data"
            )
```

---

## 🏠 Full Example: Home Assistant Integration

```python
"""
Home Assistant Tool
===================
Control smart home devices via Home Assistant.
"""

import logging
import httpx
from typing import Any, Dict

from tool_plugins import BaseTool, ToolResult, ToolStatus

logger = logging.getLogger(__name__)


class HomeAssistantTool(BaseTool):
    name = "HOME"
    description = "Control smart home devices (lights, switches, etc.)"
    enabled = True
    
    parameters = {
        "action": {
            "type": "string",
            "description": "Action: on, off, toggle, status",
            "required": True
        },
        "device": {
            "type": "string",
            "description": "Device name or entity ID",
            "required": True
        }
    }
    
    # 📋 Device name mappings
    DEVICE_MAP = {
        "living room light": "light.living_room",
        "bedroom light": "light.bedroom",
        "kitchen light": "light.kitchen",
        "front door": "lock.front_door",
        "garage": "cover.garage_door",
    }
    
    def __init__(self, assistant):
        super().__init__(assistant)
        self.ha_url = getattr(self.config, 'home_assistant_url', None)
        self.ha_token = getattr(self.config, 'home_assistant_token', None)
        
        if not self.ha_url or not self.ha_token:
            self.enabled = False
            logger.info("🏠 Home Assistant tool disabled - not configured")
    
    async def execute(self, params: Dict[str, Any]) -> ToolResult:
        action = params.get("action", "").lower()
        device = params.get("device", "")
        
        # 🔍 Resolve device name to entity ID
        entity_id = self.DEVICE_MAP.get(device.lower(), device)
        
        headers = {
            "Authorization": f"Bearer {self.ha_token}",
            "Content-Type": "application/json"
        }
        
        try:
            async with httpx.AsyncClient(timeout=10.0) as client:
                if action == "status":
                    # 📊 Get current state
                    response = await client.get(
                        f"{self.ha_url}/api/states/{entity_id}",
                        headers=headers
                    )
                    data = response.json()
                    state = data.get("state", "unknown")
                    
                    return ToolResult(
                        status=ToolStatus.SUCCESS,
                        message=f"The {device} is currently {state}"
                    )
                else:
                    # ⚡ Perform action
                    domain = entity_id.split(".")[0]
                    service = f"turn_{action}" if action != "toggle" else "toggle"
                    
                    response = await client.post(
                        f"{self.ha_url}/api/services/{domain}/{service}",
                        headers=headers,
                        json={"entity_id": entity_id}
                    )
                    
                    if response.status_code == 200:
                        return ToolResult(
                            status=ToolStatus.SUCCESS,
                            message=f"Done! The {device} is now {action}"
                        )
                    else:
                        return ToolResult(
                            status=ToolStatus.FAILED,
                            message=f"Couldn't {action} the {device}"
                        )
                        
        except Exception as e:
            logger.error(f"Home Assistant error: {e}")
            return ToolResult(
                status=ToolStatus.FAILED,
                message="Error communicating with Home Assistant"
            )
```

---

## ✅ Best Practices

### 1. 🛡️ Handle Errors Gracefully

```python
try:
    result = await some_api_call()
except Exception as e:
    logger.error(f"Error: {e}")
    return ToolResult(
        status=ToolStatus.FAILED,
        message="I couldn't complete that request"  # 👈 User-friendly!
    )
```

### 2. ✔️ Validate Input

```python
async def execute(self, params):
    value = params.get("value")
    
    if not value:
        return ToolResult(
            status=ToolStatus.FAILED,
            message="Please provide a value"
        )
    
    if len(value) > 100:
        return ToolResult(
            status=ToolStatus.FAILED,
            message="Value is too long"
        )
```

### 3. 📝 Use Logging

```python
from logging_utils import log_event

log_event(logger, logging.INFO, "Tool executed",
         event="my_tool_success", 
         data={"key": "value"})
```

### 4. 🗣️ Keep Messages Conversational

```python
# ✅ Good - natural speech
message = "The temperature is 72 degrees"

# ❌ Bad - robotic
message = "Temperature: 72°F | Humidity: 45%"
```

### 5. 🔧 Disable When Unconfigured

```python
def __init__(self, assistant):
    super().__init__(assistant)
    if not self.config.required_setting:
        self.enabled = False
        logger.info("Tool disabled - missing config")
```

---

## 📁 Plugin Directory Structure

```
src/plugins/
├── 📄 __init__.py          # Package marker
├── 📄 README.md            # Plugin documentation
├── 📄 timer_tool.py        # ⏲️ SET_TIMER
├── 📄 callback_tool.py     # 📞 CALLBACK
├── 📄 weather_tool.py      # 🌤️ WEATHER
├── 📄 hangup_tool.py       # 📴 HANGUP
├── 📄 status_tool.py       # 📋 STATUS
├── 📄 cancel_tool.py       # ❌ CANCEL
├── 📄 datetime_tool.py     # 🕐 DATETIME
├── 📄 calc_tool.py         # 🧮 CALC
├── 📄 joke_tool.py         # 😄 JOKE
└── 📄 my_custom_tool.py    # 🔌 Your plugins!
```

---

## 🧪 Testing Checklist

```bash
# 1. ✅ Tool appears in list
curl http://localhost:8080/tools | jq '.[].name' | grep MY_TOOL

# 2. ✅ Tool info is correct
curl http://localhost:8080/tools/MY_TOOL | jq

# 3. ✅ Execution works
curl -X POST http://localhost:8080/tools/MY_TOOL/execute \
  -H "Content-Type: application/json" \
  -d '{"params": {"param1": "value1"}}' | jq

# 4. ✅ Error handling works
curl -X POST http://localhost:8080/tools/MY_TOOL/execute \
  -H "Content-Type: application/json" \
  -d '{"params": {}}' | jq  # Missing required param

# 5. ✅ Voice test - make a call and try it!
```
