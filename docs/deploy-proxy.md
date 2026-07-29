# Deploy a LightSpeed Proxy

> Deploy your own zero-cost proxy relay on Vultr or Oracle Cloud Always Free tier.

## Quickstart (Vultr)

1. **Create a Vultr VPS** — the $6/mo plan has a [free trial credit](https://www.vultr.com/free/)
2. **SSH in** and run:

```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source ~/.cargo/env

# Clone and build
git clone https://github.com/ShibbityShwab/lightspeed.git
cd lightspeed
cargo build --release -p lightspeed-proxy
```

3. **Configure** — create `proxy.toml`:

```toml
[proxy]
listen_addr = "0.0.0.0:4434"
http_addr = "0.0.0.0:8080"

[auth]
enabled = false   # Set to true and add tokens for production
tokens = []

[abuse]
max_pps = 10000
max_bps = 10485760
```

4. **Run**:

```bash
sudo ./target/release/lightspeed-proxy
```

5. **Open firewall** (Vultr dashboard → Firewall → add UDP 4434 + TCP 8080)

6. **Test from your PC**:

```bash
lightspeed --proxy YOUR_VULTR_IP:4434 --game rust
```

## Oracle Cloud Always Free

Oracle's Always Free tier includes 4 ARM cores + 24 GB RAM — complete overkill for a proxy (~500 KB RAM), but it's permanent and free.

```bash
# On Oracle Linux / Ubuntu ARM64
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source ~/.cargo/env
git clone https://github.com/ShibbityShwab/lightspeed.git
cd lightspeed
cargo build --release -p lightspeed-proxy
```

Same `proxy.toml` config as above.

## Systemd Service

```ini
# /etc/systemd/system/lightspeed-proxy.service
[Unit]
Description=LightSpeed Proxy
After=network.target

[Service]
Type=simple
ExecStart=/home/ubuntu/lightspeed/target/release/lightspeed-proxy
Restart=always
RestartSec=5
User=nobody

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now lightspeed-proxy
```

## Multi-Node Mesh

For best results, deploy 2-3 nodes in different regions:

| Region | Typical RTT to Bangkok |
|--------|----------------------|
| Singapore | ~31 ms |
| Tokyo | ~85 ms |
| Los Angeles | ~206 ms |
| Frankfurt | ~170 ms |
| Sydney | ~115 ms |

Add all proxies to your `lightspeed.toml`:

```toml
[proxy]
servers = [
    "YOUR_SGP_IP:4434",
    "YOUR_LAX_IP:4434",
]
```

The client probes all proxies on startup and auto-selects the fastest route.

## Monitoring

```bash
# Health check
curl http://YOUR_PROXY_IP:8080/health

# Prometheus metrics
curl http://YOUR_PROXY_IP:8080/metrics
```
