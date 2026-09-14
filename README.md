# Lab 02: DevOps

## Setup

- Created a four-node CloudLab experiment using `multi-node-cluster`.
- Used `node0` as the load generator and `node1` as the NGINX web server.
- Created and versioned `nginx.sh` and `index.html` with Git, then pushed them to GitHub.
- Deployed NGINX remotely through SSH and copied the custom webpage through SCP.
- Configured NGINX to use one worker process.

## Baseline Test

```text
Rate: 10 RPS for 10 seconds
Requests: 100
Success: 100.00%
Mean latency: 644.083µs
P99 latency: 1.643ms
Status codes: 200:100
```

## Stress Test

```text
Requested rate: 100,000 RPS for 10 seconds
Actual attack rate: 17,111.65 RPS
Throughput: 7,101.73 RPS
Success: 88.85%
Successful responses: 161,540
TCP-level failures: 20,267
Mean latency: 1.551s
P99 latency: 14.789s
```

The system saturated under high load: latency increased sharply and some connections failed before receiving an HTTP response.

## Finding the Knee of the Curve

| Requested RPS | Success | P99 latency |
| ---: | ---: | ---: |
| 1,000 | 100.00% | 896.941µs |
| 8,000 | 100.00% | 11.106ms |
| 21,000 | 100.00% | 37.164ms |
| 22,000 | 96.42% | 1.518s |

- **Latency wall:** 8,000 RPS.
- **Saturation point:** 22,000 RPS.

## Remote Telemetry

```text
Requests: 279,796
Success: 67.88%
200 responses: 189,917
TCP-level failures: 89,879
Mean latency: 1.836s
P99 latency: 11.445s
```

The NGINX worker peaked at approximately 74% CPU. The bottleneck was therefore mainly in the TCP/network path and client-side connection resources, rather than NGINX CPU alone.
