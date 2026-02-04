# MINDMAP.md - Acme-SaaS / Pilot Production Stack

[0] **🎯 PRIME DIRECTIVE FOR AI AGENTS:** This mindmap is your primary knowledge index. Read nodes [1-9] first (they explain the system), then read overview nodes [10-14] for project context. Follow `[N]` links to navigate. **Always update this file as you work.**

[1] **Meta: Mind Map Format** - This is a graph-based documentation format where each node is one line: `[N] **Title** - content with [N] references`. The format is homoiconic—these instructions are themselves nodes demonstrating the format [2][3][4]. Nodes enable atomic line-by-line updates, grep-based search, VCS-friendly diffs, and LLM-native citation syntax [5][6].

[2] **Meta: Node Syntax** - Format is `[N] **Title** - description with [N] references`. Each node is exactly one line (use `\n` for internal breaks if needed). Titles use markdown bold `**...**`. References use citation syntax `[N]` which LLMs recognize from academic papers [1][3]. Node IDs are sequential integers starting from 1.

[3] **Meta: Node Types** - Nodes are prefixed by type: `**AE: X**` (Architecture Element), `**WF: X**` (Workflow), `**DR: X**` (Decision Record), `**BUG: X**` (Bug Record), `**TODO: X**` (Planned Work), `**Meta: X**` (Documentation about this mindmap itself) [1][2][4]. Use `**[DEPRECATED → N]**` prefix for outdated nodes that redirect elsewhere [6].

[4] **Meta: Quick Start for New Agents** - First time here? (1) Read [1-9] to understand the format, (2) Read [10-14] for project overview, (3) Grep for your task: `grep -i "auth"` then read matching nodes, (4) Follow `[N]` links to dive deeper, (5) Update nodes as you work per protocol [6][7][8].

[5] **Meta: Why This Format Works** - Line-oriented structure allows atomic updates (replace line N to update node N), instant grep lookup (`grep "^\[42\]"` finds node 42), diff-friendly changes (only edited lines change), and zero parsing overhead [1][2]. The `[N]` citation syntax leverages LLM training on academic papers—agents already know how to follow references [3].

[6] **Meta: Update Protocol** - **MANDATORY:** (1) Before starting work, grep for related nodes and read them [4], (2) After making changes, update affected nodes immediately, (3) Add new nodes only if concept is referenced 3+ times OR non-obvious from code, (4) For bug fixes create `**BUG:**` node with root cause + solution + commit hash [3], (5) For deprecation use `**[DEPRECATED → N_new]**` prefix and keep the line [3], (6) If node seems outdated mark `(verify YYYY-MM-DD)` and fix within 2 commits [7][8].

[7] **Meta: Node Lifecycle Example** - Initial: `[12] **AE: AuthService** - Handles JWT validation using jsonwebtoken [15][22]`. After refactor: `[12] **AE: AuthService** - Handles JWT validation using Passport.js [15][22][31] (updated 2026-02-02)`. After deprecation: `[12] **[DEPRECATED → 45] AE: AuthService** - Replaced by PassportAuthService [45]` [6][3].

[8] **Meta: Reality vs Mindmap** - **Critical rule:** If the mindmap contradicts the actual codebase, the code is the source of truth—but you must update the mindmap immediately to reflect reality [6]. The mindmap is an index, not a specification. Stale nodes are worse than missing nodes because they mislead future agents.

[9] **Meta: Scaling Strategy** - Small projects: <50 nodes. Medium: <100 nodes. Large: split into domain-specific files like `MINDMAP.auth.md`, `MINDMAP.payments.md` [10]. Link from main mindmap: `[15] **AE: Auth System** - See MINDMAP.auth.md for details. Uses JWT [12][22]`. Each sub-mindmap has its own [1-9] meta nodes and [10-14] overview nodes following this same format [1][3].

[10] **Project** - "Acme SaaS" production stack. 5 core services on Kubernetes in cluster: prod-us-east-1

─── System Topology ───

[16] **AE: Web Frontend** - React SPA served by Nginx. Namespace: web-prod. Ingress: https://app.acme.com. Talks to [17] over HTTPS. Used by: end users via browser

[17] **AE: API Backend** - FastAPI service. Namespace: api-prod. Base URL: http://api-backend.api-prod.svc.cluster.local:8000. Dependencies: [18] Auth Service, [22] Primary Database, [24] Redis Cache. Ingress: https://api.acme.com

[18] **AE: Auth Service** - JWT auth and session management. Namespace: auth-prod. Endpoints: /login, /refresh, /validate at http://auth-service.auth-prod.svc.cluster.local:8080. Dependencies: [23] Auth Database, [24] Redis Cache. Used by: [17]

[22] **AE: Primary Database** - PostgreSQL 15 at postgres-primary.db-prod.svc.cluster.local:5432. Namespace: db-prod. Stores core business entities (users, orgs, subscriptions). Connection pool target: max 200 connections. Used by: [17]

[23] **AE: Auth Database** - PostgreSQL 15 at auth-db.db-prod.svc.cluster.local:5432. Namespace: db-prod. Stores auth-related tables (sessions, refresh tokens, login attempts). Connection pool target: max 100 connections. Used by: [18]

[24] **AE: Redis Cache** - Redis 7 at redis-cache.cache-prod.svc.cluster.local:6379. Namespace: cache-prod. Used for API rate limiting, session cache, and feature flags. Used by: [17][18]

[26] **AE: Cluster & Namespaces** - Cluster: prod-us-east-1. Namespaces: web-prod, api-prod, auth-prod, db-prod, cache-prod. On-call SREs have kubectl access via role: sre-admin. Read-only role: sre-readonly

[27] **AE: Ingress & DNS** - External DNS via Route53. app.acme.com → web-prod ingress. api.acme.com → api-prod ingress. TLS certs via cert-manager in kube-system namespace

─── Diagnostic Workflows ───

[30] **WF: "Users can't reach app"** - Applies when users report UI doesn’t load or shows 502/504: (1) Check web ingress & pods [31]; (2) Check API health [32]; (3) Inspect recent web + API logs [33]; (4) Check API dependencies [34]; (5) Check recent changes / deployments [35]

[31] **WF: Web Ingress & Pods** - Run: `kubectl get ingress -n web-prod` and `kubectl get pods -n web-prod`; Expect: ingress has address, pods Ready=1/1; If ingress missing or no addresses: check [27]; If pods CrashLoopBackOff: follow [36]

[32] **WF: API Health** - Run: `kubectl get pods -n api-prod` and `curl -I https://api.acme.com/health`; Expect: API pods Ready=1/1 and HTTP 200 from /health; If health fails: check logs [33] and dependencies [34]

[33] **WF: Log Analysis** - Run: `kubectl logs deploy/web-frontend -n web-prod --tail=100 | grep -i "error\|timeout"` and `kubectl logs deploy/api-backend -n api-prod --tail=200 | grep -i "error\|timeout\|exception"`; Look for: upstream 5xx, connection refused [40], DB errors [43], Redis errors [44]

[34] **WF: API Dependency Check** - For [17] API Backend: check DB `kubectl exec -it deploy/api-backend -n api-prod -- nc -vz postgres-primary.db-prod.svc.cluster.local 5432`; check Redis `kubectl exec -it deploy/api-backend -n api-prod -- nc -vz redis-cache.cache-prod.svc.cluster.local 6379`; check Auth `kubectl exec -it deploy/api-backend -n api-prod -- curl -I http://auth-service.auth-prod.svc.cluster.local:8080/health`; if any dependency unhealthy: use [45] for DB, [46] for Redis, [47] for Auth

[35] **WF: Recent Changes** - Run: `kubectl get deploy -A --sort-by=.metadata.creationTimestamp | tail`; `kubectl rollout history deploy/api-backend -n api-prod`; `git log -5 --oneline` in infra repo; if incident started shortly after rollout: consider `kubectl rollout undo deploy/api-backend -n api-prod` (per team approval policy)

[37] **WF: "API is slow"** - Symptoms: p95 latency > 500ms, users report slowness: (1) Check pod resources & restarts with `kubectl top pods -n api-prod` and `kubectl get pods -n api-prod`; (2) Check metrics in dashboard [60] for CPU, memory, DB latency; (3) Check DB for slow queries [45]; (4) Check Redis hit rate and latency [46]; (5) Check external dependency latency if applicable

[45] **WF: Database Health & Performance** - For [22] or [23]: `kubectl get pods -n db-prod`; `kubectl logs statefulset/postgres-primary -n db-prod --tail=100 | grep -i "error\|timeout"`; connect with `kubectl exec -it postgres-primary-0 -n db-prod -- psql -U postgres -d appdb`; then run `SELECT COUNT(*) FROM pg_stat_activity;` and `SELECT query, now()-query_start AS duration FROM pg_stat_activity WHERE state = 'active' ORDER BY duration DESC LIMIT 5;`

[46] **WF: Redis Health** - For [24]: `kubectl get pods -n cache-prod`; `kubectl logs deploy/redis-cache -n cache-prod --tail=100 | grep -i "error\|timeout"`; `kubectl exec -it deploy/redis-cache -n cache-prod -- redis-cli ping`; if errors about maxclients or timeouts: see [48]

[47] **WF: Auth Service Issues** - For [18]: `kubectl get pods -n auth-prod`; `curl -I http://auth-service.auth-prod.svc.cluster.local:8080/health`; `kubectl logs deploy/auth-service -n auth-prod --tail=200 | grep -i "error\|timeout\|jwt"`; look for DB errors [43], Redis errors [44], or external IdP issues

─── Common Issues & Fix Patterns ───

[36] **BUG: CrashLoopBackOff** - Pod restarting repeatedly (web or API): check logs [33] for startup errors; `kubectl describe pod <pod> -n <namespace>` for events; common causes: bad config (missing env vars), DB unreachable [40], migration failures; fixes: revert config/secret or rollback deployment [35]

[40] **BUG: Connection Refused** - Service cannot reach dependency (DB, Redis, Auth): confirm dependency pods running `kubectl get pods -n <dep-namespace>`; test DNS with `kubectl exec -it <pod> -n <ns> -- nslookup <dep-service>`; test port with `kubectl exec -it <pod> -n <ns> -- nc -vz <dep-host> <port>`; check network policies, service name, port; example: Auth listens on 8080 not 80 [18]

[43] **BUG: DB Connection Pool Exhausted** - Errors like "too many connections" or "remaining connection slots are reserved": check connection count and slow queries via [45]; short term: increase app pool size up to limits (200 for [22], 100 for [23]); longer term: fix long-running queries and ensure connections are closed properly in app

[44] **BUG: Redis Timeouts / Evictions** - Errors like "MOVED", "READONLY", "timeout", "OOM command not allowed": check `redis-cli info memory` and `redis-cli info stats` via [46]; if hitting memory limit: increase memory or reduce TTLs on large keys; if timeouts: verify network policies and pod CPU/memory, and reduce per-request Redis calls

[48] **BUG: Redis Maxclients Reached** - Error "max number of clients reached": check connected clients via `redis-cli info clients`; short term: raise maxclients if resources allow; longer term: implement connection pooling and reduce idle clients in [17] and [18]

─── Observability ───

[60] **Datadog Dashboard** - URL: https://app.datadoghq.com/dashboard/acme-prod-overview; key metrics: API latency p95 (normal < 200ms, alert > 500ms for 5min), error rate (normal < 1%, alert > 5%), DB CPU and connections for [22][23], Redis CPU/memory/ops/sec for [24]

[61] **WF: Check Service Metrics** - For any AE: latency query `avg:service.request.latency{service:<name>,env:prod}.rollup(p95, 300)`; error rate query `sum:service.request.errors{service:<name>,env:prod}.as_rate()`; correlate metric spikes in [60] with recent deployments [35]
