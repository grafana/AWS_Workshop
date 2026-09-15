FROM docker.io/grafana/xk6:latest@sha256:b4ff7af030d1e905b5aa5f00eed59c4275d70258cbfb5a294e473cf497d628c8 AS builder

# Build the xk6 binary with the required extensions
RUN xk6 build --with github.com/grafana/xk6-faker@latest

FROM docker.io/library/debian:12-slim

COPY --from=builder /xk6/k6 /usr/bin/k6

COPY script.js .

ENTRYPOINT ["k6", "run", "script.js"]
