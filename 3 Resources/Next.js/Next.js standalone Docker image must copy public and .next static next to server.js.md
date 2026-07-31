---
tags: [nextjs, docker, standalone, deployment]
---

# Next.js standalone Docker image must copy public and .next/static next to server.js

With `output: "standalone"` in `next.config`, `next build` emits `.next/standalone/` — a minimal `server.js` plus only the traced production deps. But the standalone server **does not bundle** static assets. A runtime image must copy three things into place, relative to `server.js`:

```dockerfile
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
```

- Omit `.next/static` → JS/CSS chunks 404 (blank/unstyled page).
- Omit `public` → images/fonts/brand assets 404.
- Start with `CMD ["node", "server.js"]`; the server honors `PORT` and `HOSTNAME` env (set `HOSTNAME=0.0.0.0` so it's reachable outside the container).

**Gotcha for apps that shell out:** standalone only traces *Node module* deps — it can't include external CLIs the app spawns (`claude`, `gcloud`, `git`, …). For a "self-runnable" image of a local-first app you must `apt-get`/`npm -g` install those tools in the runner stage yourself, and mount the app's data/vault + supply credentials at runtime. See [[Claude Code runs on Vertex AI via three env vars with gcloud ADC]].

Related: [[Next.js standalone bundle breaks when the dot-prefixed .next folder is dropped in transfer]].
