# Exposed Metrics

## Challenge Information

* **Category:** Observability Failures
* **Difficulty:** 1
* **Challenge:** Exposed Metrics
* **Objective:** Access the application's exposed Prometheus metrics endpoint.
* **Route:** `/metrics`

---

## Objective

The objective of this challenge is to identify and access an application endpoint that exposes internal monitoring and operational metrics.

The exposed metrics provide information about the application's runtime environment, application state, users, requests, and other internal statistics.

---

## Reconnaissance / Discovery

### 1. Search the application source

The first step was to search the Juice Shop source code for references to the challenge and metrics functionality:

```bash
grep -Rni -C 8 "exposedMetrics\|metrics" /var/lib/juice-shop/build /var/lib/juice-shop/config 2>/dev/null | head -120
```

### What this command does

* `grep` searches files for matching text.
* `-R` searches recursively through directories.
* `-n` displays line numbers.
* `-i` makes the search case-insensitive.
* `-C 8` displays eight lines of context around each match.
* The search was performed against the Juice Shop build and configuration directories.

### Important finding

The search identified:

```text
/var/lib/juice-shop/build/test/cypress/e2e/metrics.spec.js
```

The test contained:

```js
cy.request('/metrics');
cy.expectChallengeSolved({ challenge: 'Exposed Metrics' });
```

This indicated that `/metrics` was the relevant endpoint.

The API tests also referenced:

```text
http://localhost:3000/metrics
```

and confirmed that the endpoint returned Prometheus-formatted metrics.

---

## Route Discovery

The next step was to determine exactly how the application registered the endpoint.

The following command was used:

```bash
grep -Rni -C 8 "metrics.*app\|app.*metrics\|/metrics" /var/lib/juice-shop/build/server.js /var/lib/juice-shop/build/routes/metrics.js 2>/dev/null
```

### Important finding

The application source contained:

```js
const metrics = __importStar(require("./routes/metrics"));
```

and:

```js
app.get('/metrics', metrics.serveMetrics());
```

This confirmed that the application explicitly exposes a `GET /metrics` route.

The metrics handler was also identified in:

```text
/var/lib/juice-shop/build/routes/metrics.js
```

The handler returns the registered Prometheus metrics:

```js
res.set('Content-Type', register.contentType);
res.end(await register.metrics());
```

This established the complete route:

```text
GET /metrics
        ↓
metrics.serveMetrics()
        ↓
register.metrics()
        ↓
Prometheus-formatted application metrics
```

---

## Challenge Verification Logic

To determine exactly what causes the challenge to be solved, the challenge-specific verification code was searched:

```bash
grep -Rni -C 8 "exposedMetricsChallenge" /var/lib/juice-shop/build/routes /var/lib/juice-shop/build/test 2>/dev/null
```

The search revealed:

```js
challengeUtils.solveIf(datacache_1.challenges.exposedMetricsChallenge, () => {
    const userAgent = req.headers['user-agent'] ?? '';
    const ignoredUserAgents = config_1.default.get('challenges.metricsIgnoredUserAgents');
    return !ignoredUserAgents.some((ignoredUserAgent) => userAgent.includes(ignoredUserAgent));
});
```

This showed that the challenge is solved when the `/metrics` endpoint is accessed by a request whose User-Agent is not included in the configured list of ignored monitoring agents.

---

## Exploitation

With the endpoint confirmed, it was accessed directly from the local Juice Shop instance:

```bash
curl -i http://127.0.0.1:42000/metrics
```

The server returned Prometheus-formatted metrics.

The response contained multiple categories of internal information.

### Application information

```text
juiceshop_version_info{version="19.1.1",major="19",minor="1",patch="1",app="juiceshop"} 1
```

This disclosed the running Juice Shop version.

### Runtime information

The endpoint exposed Node.js information:

```text
nodejs_version_info{version="v24.19.0",major="24",minor="19",patch="0",app="juiceshop"} 1
```

It also exposed process and memory statistics such as:

```text
process_resident_memory_bytes
process_virtual_memory_bytes
process_heap_bytes
process_open_fds
```

### Application statistics

The metrics also exposed application-level information including:

```text
juiceshop_users_registered_total
juiceshop_orders_placed_total
juiceshop_wallet_balance_total
juiceshop_user_social_interactions
```

For example:

```text
juiceshop_users_registered_total{app="juiceshop"} 21
juiceshop_orders_placed_total{app="juiceshop"} 3
juiceshop_wallet_balance_total{app="juiceshop"} 500
```

### Request statistics

The endpoint also exposed HTTP request counters:

```text
http_requests_count{status_code="2XX",app="juiceshop"} 195
http_requests_count{status_code="3XX",app="juiceshop"} 280
```

This demonstrates that the endpoint was not merely returning generic health information; it exposed operational and application-specific telemetry.

---

## Evidence

The challenge was successfully marked as solved after accessing the exposed `/metrics` endpoint.

### Screenshots

#### Challenge Metadata

![Exposed Metrics Challenge Metadata](images/01-exposed-metrics-challenge-metadata.png)

#### Attack Surface

![Exposed Metrics Attack Surface](images/02-exposed-metrics-attack-surface.png)

#### Exploitation Evidence

![Exposed Metrics Exploitation Evidence](images/03-exposed-metrics-exploitation-evidence.png)

The exploitation evidence screenshot documents the successful access to the `/metrics` endpoint and the resulting exposed metrics.

---

## Security Impact

An externally accessible metrics endpoint can disclose information that should normally be restricted to trusted monitoring infrastructure.

Depending on the application's implementation, exposed metrics can reveal:

* Application and dependency versions
* Runtime environment information
* CPU and memory usage
* Active connections and resources
* User statistics
* Business/application statistics
* Request statistics
* Security or challenge-related state
* Other custom application telemetry

This information can assist an attacker during reconnaissance and provide useful context about the application's internal operation.

---

## Root Cause

The root cause is the unrestricted exposure of the Prometheus metrics endpoint:

```js
app.get('/metrics', metrics.serveMetrics());
```

The application serves the endpoint without requiring authentication or restricting access to an authorized monitoring system.

---

## Lessons Learned

This challenge demonstrates that observability endpoints can become an information-disclosure vulnerability when exposed to untrusted users.

During security testing, endpoints such as:

```text
/metrics
/health
/debug
/status
/actuator
```

should be included in reconnaissance because they may expose operational information that was intended only for administrators or monitoring infrastructure.

It also demonstrates the value of source-code reconnaissance in a controlled environment. Searching for challenge references first revealed the intended endpoint, after which the server-side route and verification logic were validated before exploitation.

---

## Conclusion

The **Exposed Metrics** challenge was solved by discovering the application's `/metrics` endpoint and requesting it directly.

The endpoint returned Prometheus-formatted telemetry containing application, runtime, system, user, request, and operational information.

The complete testing workflow was:

```text
Source Reconnaissance
        ↓
Challenge / Endpoint Discovery
        ↓
Server Route Validation
        ↓
Challenge Verification Analysis
        ↓
GET /metrics
        ↓
Sensitive Metrics Exposed
        ↓
Challenge Solved
```
