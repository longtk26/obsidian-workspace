
---

## Table of Contents

1. [[#The 30-Second Mental Model]]
2. [[#Forward Proxy — The Client's Bodyguard]]
3. [[#Reverse Proxy — The Server's Bodyguard]]
4. [[#Side-by-Side Comparison]]
5. [[#Common Use Cases]]
6. [[#Key Features of a Reverse Proxy]]
7. [[#Popular Tools]]
8. [[#When to Use Which]]

---

## The 30-Second Mental Model

Think of a proxy as a **middleman** that sits between two parties and relays traffic on their behalf.

- A **forward proxy** sits on the **client side** — it hides *who is asking*.
- A **reverse proxy** sits on the **server side** — it hides *who is answering*.

```
Forward Proxy:
  [Your Browser] → [Forward Proxy] → [Internet / Target Server]
  (server sees the proxy's IP, not yours)

Reverse Proxy:
  [Your Browser] → [Reverse Proxy] → [Backend Server(s)]
  (you see the proxy's IP, not the real server's)
```

---

## Forward Proxy — The Client's Bodyguard

A forward proxy acts on behalf of **clients** (users/browsers). When you send a request, it goes to the proxy first, which then forwards it to the destination.

### What it does
- **Hides your IP** — the destination server only sees the proxy's address.
- **Bypasses restrictions** — route around geo-blocks or corporate firewalls.
- **Content filtering** — organisations use it to block certain sites for internal users.
- **Caching** — shared cache across an office network reduces bandwidth.

### Real-world analogy
You ask a colleague to Google something on your behalf during an interview. The interviewer never knows it was you searching — they just see your colleague.

### Where you see it
- Corporate office networks (all traffic routed through a company proxy)
- VPNs (your VPN server is essentially a forward proxy)
- Tools like `Squid`, `Privoxy`

---

## Reverse Proxy — The Server's Bodyguard

A reverse proxy sits in front of **one or more backend servers**. Every incoming request hits the proxy first, which then decides where to send it.

### How it works — step by step

```
1. Client sends a request to yourdomain.com
2. DNS resolves to the reverse proxy's IP (not the real server)
3. Reverse proxy receives the request
4. It picks a healthy backend server to forward the request to
5. Backend processes and responds to the proxy
6. Proxy sends the response back to the client
```

The client never knows the real server's IP — it only ever talks to the proxy.

### Real-world analogy
You call a company's main number. A receptionist (the proxy) answers, decides which department (backend server) to transfer you to, and you get your answer — but you never dialled the department directly.

---

## Side-by-Side Comparison

| Aspect | Forward Proxy | Reverse Proxy |
|---|---|---|
| **Protects** | Clients | Servers |
| **Hides** | Client IP / identity | Server IP / identity |
| **Sits in front of** | Clients | Backend servers |
| **Initiated by** | Client configures it | Server admin configures it |
| **Typical users** | End users, corporate networks | DevOps, platform engineers |
| **Examples** | VPN, Squid | Nginx, HAProxy, Traefik |

---

## Common Use Cases

### Forward Proxy
- **Bypass geo-restrictions** — access content blocked in your region
- **Corporate internet control** — IT departments allow/block specific sites
- **Anonymity & privacy** — hide user identity from external services
- **Bandwidth saving** — shared cache in a large office

### Reverse Proxy
- **High-traffic sites** — spread load across multiple servers so no single one gets overwhelmed
- **Microservices routing** — one public IP, many internal services; the proxy routes by path or hostname
- **Security hardening** — the real servers are never exposed to the public internet
- **Multi-domain hosting** — `api.example.com` and `app.example.com` can live on different servers behind one IP
- **CDN edge nodes** — every CDN PoP is essentially a reverse proxy caching your content globally

---

## Key Features of a Reverse Proxy

### Load Balancing
Distributes incoming traffic across multiple backend instances. If one server goes down, the proxy stops routing to it. Common strategies: round-robin, least connections, IP hash.

```
           ┌─────────────────┐
Request →  │  Reverse Proxy  │ → Server A  (healthy)
           │  (load balancer)│ → Server B  (healthy)
           └─────────────────┘ ✗ Server C  (down — skipped)
```

### SSL Termination
The proxy handles the TLS handshake and decryption. Backend servers receive plain HTTP, which:
- Reduces CPU load on each backend
- Centralises certificate management (renew in one place)

### Caching
Stores responses for frequently requested resources. Subsequent identical requests are served directly from the proxy — the backend never gets hit.

### Request Filtering / WAF
Acts as a first line of defence — block bad bots, rate-limit IPs, reject malformed requests before they ever reach your application code.

### Compression
Compresses responses (gzip/brotli) at the proxy level so backends don't have to.

---

## Popular Tools

| Tool | Best For | Notes |
|---|---|---|
| **Nginx** | General-purpose, high concurrency | Lightweight, widely used, great docs |
| **Apache (mod_proxy)** | Full-featured traditional setups | Highly configurable, larger footprint |
| **HAProxy** | Enterprise load balancing | Battle-tested, excellent observability |
| **Traefik** | Docker / Kubernetes native | Auto-discovers services, built-in Let's Encrypt |
| **Caddy** | Simple HTTPS setup | Automatic SSL, zero-config friendly |

---

## When to Use Which

```
Are you trying to hide WHO IS ASKING?
  → Use a Forward Proxy

Are you trying to hide WHO IS ANSWERING or distribute server load?
  → Use a Reverse Proxy

Building a microservices backend with multiple services?
  → Reverse Proxy (API Gateway pattern)

Employees browsing the internet through a company network?
  → Forward Proxy

Running a public-facing web app?
  → Reverse Proxy (Nginx / Traefik in front of your app)
```
