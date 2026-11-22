---
layout: post
title: "One latency number hides the slow part"
description: Why I break HTTP requests into DNS, TCP, TLS, TTFB and transfer before blaming the app.
---

"The API is slow" usually arrives with a single number attached. 900ms. Maybe
a p99 from a dashboard. That number is real, but it is a sum, and the fix
depends entirely on which part of the sum grew.

## A request is a pipeline

Every HTTPS request walks through the same phases, in order:

| Phase | What is happening | Usually owned by |
|---|---|---|
| DNS | Resolving the hostname | Resolver, DNS provider, TTLs |
| TCP | Three-way handshake | Network path, distance, load balancer |
| TLS | Handshake and certificate exchange | LB/proxy config, cert chain, TLS version |
| TTFB | Server receives the request and starts responding | The application and whatever it calls |
| Transfer | Body bytes arrive | Payload size, bandwidth, compression |

Only one of those rows is the application. If DNS jumps from 5ms to 400ms
because a resolver is timing out and retrying, no amount of profiling the
service will find it.

## curl already tells you

You do not need special tooling to start. `curl` exposes cumulative timings
through `-w`:

```bash
curl -so /dev/null https://example.com/ -w '
dns      %{time_namelookup}
connect  %{time_connect}
tls      %{time_appconnect}
ttfb     %{time_starttransfer}
total    %{time_total}
'
```

Each value is seconds since the start, so the phase itself is the difference
from the line above. A made-up but typical bad day looks like this:

```text
dns      0.412
connect  0.431
tls      0.478
ttfb     0.602
total    0.611
```

Total is 611ms, and two thirds of it is name resolution. The server answered
in about 124ms once it had the request. The ticket that said "the API is
slow" was a DNS problem.

## Streaming changes the question

For streaming responses, like server-sent events or LLM token streams, TTFB
and total stop being enough. A stream can start fast and then stall between
chunks. What matters is the gap between chunks, often called inter-token
latency (ITL), and how it is distributed. A smooth 30ms cadence and a stream
that pauses for two seconds halfway through can share the same total.

## Watching it live

I wanted this broken out per phase and printed as each phase completes,
including chunk timing for streams, so I built
[check-endpoint](https://bytebeast.github.io/check-endpoint/). Point it at an
endpoint and you see where the time goes while the request is still in
flight.

The habit is the useful part, whatever tool you use. Before anyone opens a
profiler, split the number.
