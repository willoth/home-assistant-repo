# Standalone Docker Build for winet-extractor

## Why this fork exists

This fork adds a standalone Dockerfile (`Dockerfile.standalone`) that allows building and running winet-extractor without Home Assistant addon dependencies.

The original `Dockerfile` requires Home Assistant's `BUILD_FROM` argument and addon infrastructure. This standalone version uses a standard Node.js Alpine base image with s6-overlay for init system compatibility.

## Key changes

- **Dockerfile.standalone**: New standalone Dockerfile
- Uses `node:20-alpine` as base image
- Includes s6-overlay v3.1.6.2 for init system
- Properly handles TypeScript compilation during build
- Environment variable: `S6_KEEP_ENV=1` required to preserve custom env vars through s6-overlay

## Building

```bash
docker build -t winet-extractor:latest -f Dockerfile.standalone .
```

## Running

```bash
docker run -d \
  --name winet-extractor \
  --restart unless-stopped \
  -e S6_KEEP_ENV=1 \
  -e WINET_HOST=192.168.3.88 \
  -e MQTT_URL=mqtt://mqttuser:mqttpassword@192.168.3.139 \
  -e MQTT_PREFIX=homeassistant \
  -e POLL_INTERVAL=10 \
  winet-extractor:latest
```

## Docker Compose / Portainer Stack

```yaml
version: '3.8'

services:
  winet-extractor:
    image: winet-extractor:latest
    container_name: winet-extractor
    restart: unless-stopped
    environment:
      - S6_KEEP_ENV=1
      - WINET_HOST=192.168.3.88
      - MQTT_URL=mqtt://mqttuser:mqttpassword@192.168.3.139
      - MQTT_PREFIX=homeassistant
      - WINET_USER=
      - WINET_PASS=
      - POLL_INTERVAL=10
      - ANALYTICS=true
```

## Upstream

Original project: https://github.com/NickStallman/home-assistant-repo
