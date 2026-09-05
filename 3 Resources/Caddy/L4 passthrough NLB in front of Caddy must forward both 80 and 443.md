---
title: "L4 passthrough NLB in front of Caddy must forward both :80 and :443"
created: 2026-08-19
type: lesson
status: seedling
source: "leo-customer360 deployments/proxy, 2026-08"
tags: [caddy, load-balancer, tls, lets-encrypt, vngcloud]
---

# L4 passthrough NLB in front of Caddy must forward both :80 and :443

An L4 network load balancer (TCP passthrough, no TLS) can front a Caddy reverse proxy running on a backend box to get HTTPS + host/path routing at near-zero cost (Caddy runs on a box you already pay for). The LB just passes TCP through.

**Requirements for Caddy auto-TLS (Let's Encrypt HTTP-01) to work behind it:**
1. DNS: the public host resolves (A record) to the LB public IP.
2. The LB forwards **:80** to Caddy — HTTP-01 challenge (`/.well-known/acme-challenge/...`) AND the HTTP→HTTPS redirect ride :80.
3. The LB forwards **:443** to Caddy for real traffic.
4. Caddy is running with a persistent `/data` volume (stores the ACME account + issued certs across restarts).

Until DNS + :80 + :443 all line up, Caddy runs but serves no HTTPS — safe to stage the container early. Run Caddy `--network host` so it binds :80/:443 and can reach `127.0.0.1:<svc>` co-located containers.

Tip: validate/format a Caddyfile without installing Caddy via a throwaway container — `docker run --rm -v $PWD/Caddyfile:/f caddy:2-alpine caddy validate|fmt /f` (env placeholders `{$VAR}` need `-e` set for validate).

Related: [[Caddy handle_path strips the path prefix, handle keeps it]]

## Related

- [[Caddy handle_path strips the path prefix]]
- [[handle keeps it]]
