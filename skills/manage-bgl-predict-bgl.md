---
name: Retrieve blood-glucose predictions for a subscriber
description: Log in, read the user's ratios/settings, then fetch hours-ahead blood-glucose predictions.
api: openapi/manage-bgl-openapi.yml
operations: [login, getSettings, predict]
---

# Retrieve blood-glucose predictions for a subscriber

Operating instructions for an agent using the Jade Diabetes prediction feature.
Predictions are a **subscriber-only** capability. All requests are HTTPS-only
against `https://app.jadediabetes.com/api/1.0`.

## Steps

1. **Authenticate** — `POST /login` (`login`) with email and password; capture
   the client login token and pass it as `token` on subsequent calls.
2. **Read settings** — `GET /settings` (`getSettings`) with the `token` to load
   the user's ratios and dose configuration that inform predictions.
3. **Predict** — `POST /predict` (`predict`) with the `token` to retrieve
   forecast blood glucose levels hours ahead.

## Rules

- The `predict` operation requires an active subscription; a non-subscriber
  token will be rejected.
- A missing/expired token returns **401** — re-authenticate via `login`.
- Treat **503** as a rate-limit signal; back off and retry.
- This is diabetes-management data — never present predictions as clinical
  advice; they supplement, not replace, the user's care team.
