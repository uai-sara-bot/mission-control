# Railway Deployment — Mission Control + OpenClaw

## Architecture

```
┌──────────────────────────────────────────────────┐
│                Railway Project                     │
│                                                    │
│  ┌─────────────────┐    ┌──────────────────────┐  │
│  │ Mission Control  │───►│  OpenClaw (existing)  │  │
│  │ (this service)   │ WS │  Gateway :8080        │  │
│  │ SQLite + Volume  │    │                       │  │
│  │ :3000            │    │                       │  │
│  └─────────────────┘    └──────────────────────┘  │
│         │                                          │
│    [Volume: /app/.data]                            │
│    (SQLite DB persisted)                           │
└──────────────────────────────────────────────────┘
```

## Required Environment Variables

| Variable | Value | Description |
|----------|-------|-------------|
| `PORT` | `3000` | Server port |
| `AUTH_USER` | `admin` | Admin username |
| `AUTH_PASS` | *(set your own)* | Admin password |
| `OPENCLAW_GATEWAY_HOST` | *(your OpenClaw public URL, no https://)* | e.g. `openclaw-production-1134.up.railway.app` |
| `OPENCLAW_GATEWAY_PORT` | `443` | Use 443 for public Railway URLs |
| `OPENCLAW_GATEWAY_TOKEN` | *(your gateway auth token)* | From openclaw.json gateway.auth.token |
| `NEXT_PUBLIC_GATEWAY_HOST` | *(same as OPENCLAW_GATEWAY_HOST)* | Browser needs to reach gateway too |
| `NEXT_PUBLIC_GATEWAY_PORT` | `443` | Public HTTPS port |
| `NEXT_PUBLIC_GATEWAY_PROTOCOL` | `wss` | Secure WebSocket |
| `MC_ALLOWED_HOSTS` | *(your MC Railway URL, no https://)* | e.g. `mission-control-production-xxxx.up.railway.app` |
| `MC_ALLOW_ANY_HOST` | `true` | Set initially, then restrict with MC_ALLOWED_HOSTS |

## Volume Setup

Add a Railway volume mounted at `/app/.data` — this persists the SQLite database.

## OpenClaw Gateway Config

Add your MC URL to `gateway.controlUi.allowedOrigins` in `openclaw.json`:
```json
{
  "gateway": {
    "controlUi": {
      "allowedOrigins": [
        "https://mission-control-production-xxxx.up.railway.app"
      ]
    }
  }
}
```

## Steps

1. Deploy this repo as a new Railway service in your existing project
2. Add a volume mounted at `/app/.data`
3. Set the environment variables above
4. Update your OpenClaw config to allow the MC origin
5. Restart OpenClaw gateway
6. Open MC URL → `/setup` to create admin account
