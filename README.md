# sdwan-sin-iccp

Python simulator for the end-to-end communication analysis presented in the paper:

> **Performance Analysis of a Hybrid and Resilient SD-WAN for Mission-Critical Applications in the Brazilian Electric Power Sector**
> M. P. L. H. Barbosa, M. H. C. Dias, and G. F. C. de Queiroz.

The simulator models the transmission of ICCP (IEC 60870-6) messages from a substation SCADA server to the operator's control center over a Wide Area Network, comparing legacy and SD-WAN architectures (with and without hardware replication) under different network degradation conditions.

---

## Overview

The communication is evaluated over real topologies:

- **Agent / Agent–Operator SD-WAN:** RNP Ipê backbone (28 nodes, 48 links).
- **Operator SD-WAN:** ONS network (4 nodes, 6 links).

Four network architectures are compared:

| Code | Architecture |
|------|--------------|
| `L`  | Legacy network (no replication) |
| `L'` | Legacy network with server/link replication |
| `S`  | Hybrid SD-WAN (no replication) |
| `S'` | Hybrid SD-WAN (with replication) |

Two transmission paths are considered: **C1** (substation → operator, direct) and **C2** (substation → agent → operator).

The reported metrics and their normative requirements (IEC 60870-6 / ONS network procedures) are:

| Metric | Requirement |
|--------|-------------|
| Delay        | < 140 ms |
| Jitter       | < 20 ms  |
| Packet loss  | < 1 %    |
| Availability | > 99.9 % |

---

## Requirements

- Python **3.12.3**
- [`networkx`](https://networkx.org/) **2.8.8**
- `numpy`
- `scipy`

Install the dependencies with:

```bash
pip install -r requirements.txt
```

`requirements.txt`:

```
networkx==2.8.8
numpy
scipy
```

---

### Simulation scenarios

The scenarios represent increasing levels of network degradation (Table III of the paper). All scenarios run 5000 simulation rounds.

| Scenario | BER (bit error rate) | Routing problem rate | Failure duration |
|----------|----------------------|----------------------|------------------|
| **A**    | 10⁻⁹                 | 10⁻⁴                 | 5 s              |
| **B**    | 10⁻⁷                 | 10⁻³                 | 10 s             |
| **C**    | 10⁻⁵                 | 10⁻²                 | 15 s             |

These parameters are defined in the `SCENARIOS` dictionary at the top of the script, so they can be inspected or adjusted in a single place.

---

## Output

For each architecture (`L`, `L'`, `S`, `S'`) and path (`C1`, `C2`), the script reports the sample mean and the 95% confidence interval for delay, jitter, packet loss, and availability. At the end, it prints the network calibration summary, including the estimated mean RTT and the TCP timeout interval (computed following RFC 6298, since ICCP runs over TCP).

---

## Reproducibility notes

- The timeout interval is calibrated from 100 initial rounds of round-trip-time sampling, following RFC 6298.
- Substation, agent, and operator positions are randomly drawn (uniform) over the topology, so individual runs vary; results converge over the 5000 rounds.
- The experiments in the paper were run on Ubuntu 24.04.1 LTS (Intel Xeon Gold 6348 @ 2.60 GHz, 4 cores, 8 GB RAM).

---
