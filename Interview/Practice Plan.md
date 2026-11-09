# Fullstack Interview Practice Plan

## How this works
- Ongoing rotating curriculum — no fixed interview date, just steady daily practice.
- ~1-2 hours/day: one primary topic, plus a quick coding drill when the cadence calls for it.
- Ask "what should I review today" and I'll pick the next item using the cadence below plus [[Practice Log]] (what you last covered and how confident you felt).
- Weighting reflects your self-assessment: Backend is review/polish (lighter, less frequent), Frontend and DevOps are ground-up (appear more often), Coding and System Design recur regularly, Soft skills weekly.
- Checkboxes track first-pass coverage. Once a track is fully checked, we loop back starting from the oldest / lowest-confidence entries in the log rather than "done forever."

## Cadence (10-day rotating cycle)
1. Backend (review/polish)
2. Frontend fundamentals
3. Coding practice (DS&A)
4. DevOps – Docker
5. Frontend
6. System Design (mock question)
7. DevOps – Kubernetes
8. Backend (review/polish)
9. Coding practice
10. Soft skills / behavioral + mock-interview recap

If the day's queued topic is already fully checked off, skip to the next unchecked item in that same track. If everything is checked, pick the item logged longest ago or with the lowest confidence score.

## Backend (strong — review & polish)
- [x] Databases: indexing, transactions/isolation levels, [[Table Partitioning]]
- [ ] Caching strategies (cache-aside, write-through, invalidation)
- [ ] Message queues & event-driven architecture — [[Message Queue]], [[Microservices/Message Queues/Kafka]], [[RabbitMQ]]
- [ ] Change Data Capture — [[Overview CDC]], [[Debezium]]
- [ ] gRPC vs REST vs GraphQL — [[gRPC]]
- [ ] Websockets — [[Overview websocket]]
- [ ] Search engines — [[ElasticSearch]]
- [ ] API design: pagination, versioning, idempotency, rate limiting
- [ ] Auth: OAuth2/OIDC, JWT, session vs token
- [ ] Concurrency & async patterns in your primary language
- [ ] SQL query optimization / EXPLAIN plans

## Frontend (weak/new — build from the ground up)
- [x] HTML/CSS fundamentals: box model, flexbox/grid, specificity
- [x] JavaScript core: closures, prototypes, event loop, `this`, promises/async-await
- [x] TypeScript essentials: generics, utility types, structural typing
- [ ] React fundamentals: component model, hooks (useState/useEffect/useMemo/useCallback), reconciliation
- [ ] State management (Context, Redux/Zustand, or equivalent)
- [ ] Rendering strategies: CSR vs SSR vs SSG, hydration
- [ ] Browser fundamentals: rendering pipeline, reflow/repaint, event loop vs Node's
- [ ] Networking from the browser: fetch, CORS, cookies, caching headers
- [ ] Web performance: Core Web Vitals, lazy loading, bundling/code splitting
- [ ] Accessibility (a11y) basics
- [ ] Testing: unit (Jest/Vitest), component (RTL), e2e (Playwright/Cypress)
- [ ] Frontend security: XSS, CSRF, CSP basics

## DevOps – Docker (weak/new — fundamentals + hands-on)
- [x] Images vs containers, layers, build caching
- [ ] Writing a good Dockerfile (multi-stage builds, slim base images)
- [ ] docker-compose for local multi-service dev
- [ ] Networking: bridge networks, exposed ports, container-to-container DNS
- [ ] Volumes & persistence
- [ ] Hands-on: containerize one of your own backend services end-to-end

## DevOps – Kubernetes (weak/new — fundamentals + hands-on)
- [ ] Core objects: Pod, Deployment, ReplicaSet, Service, Ingress — [[Kubernetes]]
- [ ] ConfigMaps & Secrets
- [ ] Resource requests/limits, HPA (autoscaling)
- [ ] Health checks: liveness/readiness/startup probes
- [ ] Rolling updates & rollbacks
- [ ] Namespaces, RBAC basics
- [ ] Ingress vs [[Reverse Proxy & Forward Proxy]] — how they relate
- [ ] Hands-on: deploy a small app to a local cluster (kind/minikube)

## System Design (moderate — apply, don't just read)
- [ ] Core building blocks — [[Overview]] (System design)
- [ ] Practice: design a URL shortener
- [ ] Practice: design a rate limiter
- [ ] Practice: design a news feed / chat system (ties into [[Overview websocket]])
- [ ] Practice: design a scalable file storage / CDN
- [ ] Trade-off talking points: consistency vs availability, SQL vs NoSQL, sharding strategies

## Coding / DS&A (moderate — keep sharp)
- [ ] Arrays/strings/two-pointer drills
- [ ] Hashmaps & sets
- [ ] Trees & graphs (BFS/DFS)
- [ ] Recursion & DP
- [ ] Follow the existing [[Road map]] for sequencing
- [ ] Timed mock: 1-2 medium LeetCode-style problems per coding session

## Soft skills / Behavioral
- [ ] Fill out [[Introduce]] — your intro/pitch
- [ ] STAR stories for: conflict, failure, leadership, ambiguity — [[Soft skill]]
- [ ] Questions to ask the interviewer
- [ ] Mock interview recap: what went well / what to fix
