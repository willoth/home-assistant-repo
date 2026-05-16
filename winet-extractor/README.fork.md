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

## Configuration

Before running, you need:
- **WINET_HOST**: IP address of your Sungrow inverter (e.g., 192.168.1.100)
- **MQTT_URL**: MQTT broker connection string in format `mqtt://username:password@broker-ip:port`
- **MQTT_PREFIX**: Topic prefix for Home Assistant auto-discovery (typically `homeassistant`)
- **POLL_INTERVAL**: How often to poll the inverter in seconds (recommended: 10)

## Running

```bash
docker run -d \
  --name winet-extractor \
  --restart unless-stopped \
  -e S6_KEEP_ENV=1 \
  -e WINET_HOST=<INVERTER_IP> \
  -e MQTT_URL=mqtt://<MQTT_USER>:<MQTT_PASSWORD>@<MQTT_BROKER_IP> \
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
      - WINET_HOST=<INVERTER_IP>
      - MQTT_URL=mqtt://<MQTT_USER>:<MQTT_PASSWORD>@<MQTT_BROKER_IP>
      - MQTT_PREFIX=homeassistant
      - WINET_USER=
      - WINET_PASS=
      - POLL_INTERVAL=10
      - ANALYTICS=true
```

### Multiple Inverters

To monitor multiple inverters, create separate services:

```yaml
version: '3.8'

services:
  winet-inverter-1:
    image: winet-extractor:latest
    container_name: winet-inverter-1
    restart: unless-stopped
    environment:
      - S6_KEEP_ENV=1
      - WINET_HOST=<INVERTER_1_IP>
      - MQTT_URL=mqtt://<MQTT_USER>:<MQTT_PASSWORD>@<MQTT_BROKER_IP>
      - MQTT_PREFIX=homeassistant
      - POLL_INTERVAL=10

  winet-inverter-2:
    image: winet-extractor:latest
    container_name: winet-inverter-2
    restart: unless-stopped
    environment:
      - S6_KEEP_ENV=1
      - WINET_HOST=<INVERTER_2_IP>
      - MQTT_URL=mqtt://<MQTT_USER>:<MQTT_PASSWORD>@<MQTT_BROKER_IP>
      - MQTT_PREFIX=homeassistant
      - POLL_INTERVAL=10
```

## MQTT Topics

Data is published to MQTT topics in the format:
<MQTT_PREFIX>/sensor/SH10RT20_<SERIAL_NUMBER>/<SENSOR_NAME>

Each inverter publishes 66 sensors with real-time data updated every POLL_INTERVAL seconds.

## Upstream

Original project: https://github.com/NickStallman/home-assistant-repo
