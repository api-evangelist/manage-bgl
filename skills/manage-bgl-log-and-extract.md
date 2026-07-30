---
name: Authenticate, log a reading, and extract diabetes data
description: Log in to Jade Diabetes, record a diabetes log entry, then pull data back by date range.
api: openapi/manage-bgl-openapi.yml
operations: [login, addLog, extract, logout]
---

# Authenticate, log a reading, and extract diabetes data

Operating instructions for an agent using the Jade Diabetes REST API (v1.0). All
requests are HTTPS-only against `https://app.jadediabetes.com/api/1.0`. Append
`.json` to any endpoint to receive JSON (the default is XML).

## Steps

1. **Authenticate** — `POST /login` with the user's email and password. Capture
   the returned client login token (varchar 40). Pass it as the `token`
   parameter on every subsequent request. For sensitive data prefer the
   OAuth-style `POST /get_token.html`.
2. **Log a reading** — `POST /add` (`addLog`) with the `token`, the log type
   (e.g. blood glucose, insulin, carbohydrate), the value, and a UTC timestamp
   in `YYYY-MM-DD HH:ii:ss`. Include a client-supplied GUID so re-sends
   de-duplicate. Use `POST /add_logs` (`addLogs`) to submit several at once.
3. **Extract data** — `GET /extract` (`extract`) with the `token` and a UTC
   start/end date range to read the entries back. Use `GET /log_range`
   (`logRange`) first if you need the available date bounds.
4. **Log out** — `POST /logout` (`logout`) to invalidate the token when done.

## Rules

- Always send the `token` parameter; a missing/expired token returns **401** —
  re-authenticate via `login`.
- Handle **503** as a rate-limit signal (per-app and per-user); back off and retry.
- Timestamps are UTC in `YYYY-MM-DD HH:ii:ss`.
- Errors return standard HTTP status codes with a JSON detail body — see
  `errors/manage-bgl-problem-types.yml`.
