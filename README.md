# Home Assistant Repo - willoth's Fork

This is a fork of [NickStallman/home-assistant-repo](https://github.com/NickStallman/home-assistant-repo) with standalone Docker support.

## Why this fork exists

Added standalone Docker build capability for winet-extractor that works without Home Assistant addon infrastructure.

## Modifications

### winet-extractor
- **Dockerfile.standalone**: Standalone Docker build using node:20-alpine
- **README.fork.md**: Complete documentation for standalone deployment
- Works with standard Docker and Portainer stacks
- No Home Assistant dependencies required

## Original Addons

* __[Sungrow WiNet S/WiNet S2 Extraction Tool](https://github.com/willoth/home-assistant-repo/tree/main/winet-extractor)__

## Upstream

Original repository: https://github.com/NickStallman/home-assistant-repo
