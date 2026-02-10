# Codex X Blocker

Blocks distracting sites (twitter.com, x.com) when Codex isn't actively working. Unblocks when a Codex session is running.

## How it works

```
┌─────────────────────┐
│   Codex Session     │
└────────┬────────────┘
         │ file changes
         ▼
┌─────────────────────┐
│     watchdog.ts     │ Watches the codex session data
└────────┬────────────┘
         │ HTTP GET /block or /unblock
         ▼
┌─────────────────────┐
│     server.ts       │  HTTP connection with watchdog
│     (port 3000)     │  and WS connection with the extension
└────────┬────────────┘
         │ WebSocket message
         ▼
┌─────────────────────┐
│   background.js     │  Chrome extension service worker
│                     │  Gets data and forwards to the content.js in tabs
└────────┬────────────┘
         │ chrome.tabs.sendMessage
         ▼
┌─────────────────────┐
│    content.js       │  Injected on twitter.com / x.com
│                     │  Shows/hides fullscreen overlay
└─────────────────────┘
```

## Requirements

- [Bun](https://bun.com)
- Google Chrome

## Setup

### 1. Backend

```bash
cd backend
cp .env.example .env
# edit .env with your CODEX_SESSION_PATH
bun install
```

### 2. Run

Terminal 1 — server:
```bash
cd backend
bun run server.ts
```

Terminal 2 — watchdog:
```bash
cd backend
bun run watchdog.ts
```

### 3. Chrome Extension

1. `chrome://extensions/` → enable **Developer mode**
2. **Load unpacked** and select `extension/`
3. Go to x.com

## .env

```
PORT=3000
CODEX_SESSION_PATH=/Users/<your-username>/.codex/sessions
```

## Structure

```
backend/
  server.ts       WS + HTTP server
  watchdog.ts     Codex session file watcher
extension/
  manifest.json   MV3 config
  background.js   Service worker, WS client
  content.js      Overlay UI on blocked sites
```
