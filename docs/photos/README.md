# Screenshots

This directory contains screenshots used in the documentation.

## Required Screenshots

Please add the following screenshots:

### Overview (index.md)
- [ ] `architecture.png` - Architecture diagram showing SIP phone -> Agent -> LLM/STT/TTS flow
- [ ] `conversation-flow.png` - Log viewer showing a weather query conversation
- [ ] `demo-video.png` - Video thumbnail or animated GIF of a call in progress

### Getting Started (getting-started.md)
- [ ] `health-check.png` - Terminal showing curl health check with successful response
- [ ] `test-call.png` - Softphone/IP phone calling the assistant extension
- [ ] `sip-registered.png` - Health endpoint showing sip_registered: true
- [ ] `tools-list.png` - JSON output of GET /tools
- [ ] `freepbx-extension.png` - FreePBX extension configuration page
- [ ] `log-viewer.png` - view-logs.py showing a conversation with tool use

### Configuration (configuration.md)
- [ ] `tempest-api.png` - Tempest weather station API token page
- [ ] `grafana-dashboard.png` - Grafana dashboard showing call metrics

### API Reference (api-reference.md)
- [ ] `swagger-ui.png` - FastAPI auto-generated Swagger docs at /docs

### Tools (tools.md)
- [ ] `tools-demo.png` - Log viewer showing tool execution (timer or weather)

### Plugins (plugins.md)
- [ ] `plugin-code.png` - VS Code with a plugin file open
- [ ] `plugin-test.png` - Terminal showing plugin test via curl

### Examples (examples.md)
- [ ] `integrations.png` - Diagram showing integrations (Home Assistant, n8n, cron)
- [ ] `scheduled-weather.png` - Log viewer showing scheduled weather call executing
- [ ] `home-assistant-automation.png` - Home Assistant automation UI
- [ ] `n8n-workflow.png` - n8n workflow with webhook and HTTP request nodes
- [ ] `grafana-alerting.png` - Grafana alerting contact point configuration

## Screenshot Guidelines

1. **Resolution**: 1200px wide minimum, 2x for retina displays
2. **Format**: PNG for UI screenshots, GIF for animations
3. **Annotations**: Use arrows/highlights sparingly, keep clean
4. **Sensitive data**: Blur or redact any passwords, API keys, IP addresses
5. **Dark mode**: Prefer light mode for better visibility in docs

## Creating Screenshots

### Terminal screenshots
```bash
# Use a tool like carbon.now.sh or just take a clean screenshot
# Ensure font is readable (14px minimum)
```

### Grafana dashboards
```bash
# Use Grafana's built-in screenshot feature
# Or use browser screenshot with dev tools closed
```

### Log viewer
```bash
# Run: python tools/view-logs.py -f
# Capture during an active call or replay from log file
```