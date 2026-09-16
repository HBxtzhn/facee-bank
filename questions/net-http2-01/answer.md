## End-to-end flow

1. **URL parsing** — scheme/host/port/path resolved; default port 443 for HTTPS.
2. **Cache lookups** (short-circuits): browser cache → Service Worker → OS DNS cache → hosts file.
3. **DNS resolution** — recursive resolver → root → TLD → authoritative; A/AAAA record returned. Cached by TTL.
4. **TCP handshake** — SYN / SYN+ACK / ACK; if HTTP/3, a QUIC handshake over UDP instead.
5. **TLS handshake** — ClientHello (SNI, ALPN), certificate verification, key exchange; TLS 1.3 takes 1 RTT (0-RTT on resumption).
6. **HTTP request** — headers (Host, Cookie, Accept-Encoding), optional body.
7. **Server processing** — routing, auth, business logic, DB/cache access.
8. **Response** — status line, headers (Cache-Control, ETag), body; possibly via CDN edge.
9. **Rendering** — HTML parsing → DOM, CSSOM, render tree, layout, paint, composite; subresources trigger more requests.
10. **Connection teardown** — keep-alive by default; FIN/ACK when idle timeout hits.

## Where latency usually hides

| Stage | Typical cost | Where to optimize |
|---|---|---|
| DNS | 20–120 ms cold | Preconnect / dns-prefetch |
| TCP + TLS | 2–3 RTT | TLS 1.3, session resumption, edge termination |
| TTFB | server-bound | caching, DB indexes, CDN |
| Subresources | often the largest | compression, HTTP/2 multiplexing, asset budget |

**Note:** the first three stages are almost entirely avoidable on repeat visits — that is why caching correctness matters more than micro-optimizing step 7.
