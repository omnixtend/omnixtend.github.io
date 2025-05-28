---
title: Architecture
nav_order: 3
---

# Architecture
{: .no_toc }

This section introduces the architectural design of the OmniXtend protocol and its core components.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 🧩 Layered Architecture

OmniXtend is built as a layered, modular stack to facilitate memory disaggregation and cache-coherent communication across compute and memory nodes **directly over the MAC layer**, bypassing higher-level protocols like UDP or TCP.

```
+----------------------------+
| Application / Software    |
+----------------------------+
| TileLink (Coherence Layer)|
+----------------------------+
| Packetizer / Depacketizer |
+----------------------------+
| Ethernet MAC Layer        |
+----------------------------+
| Physical Ethernet         |
+----------------------------+
```

---

## 🔄 TileLink Coherence Layer

- OmniXtend uses **TileLink**, an open-source cache coherence protocol, to support shared memory operations.
- Master (initiator) and Slave (responder) endpoints exchange `A`, `B`, `C`, `D`, and `E` channel messages.
- The protocol ensures **cache-coherent reads and writes** across networked nodes.

---

## 📦 Packet Format

TileLink messages are encapsulated directly into OmniXtend Ethernet frames structured as:

```
+-------------------+
| Ethernet Header   |
+-------------------+
| OmniXtend Header  |
+-------------------+
| TileLink Payload  |
+-------------------+
```

- **No TCP/IP or UDP is used** — packets are exchanged at the **MAC layer**.
- **OmniXtend Header** includes:
  - Source/Destination node IDs
  - Channel type (A, B, C, D, E)
  - Transaction ID
  - Length

---

## 🧠 Memory Node (Responder)

- Receives TileLink packets over Ethernet MAC
- Decodes and services memory requests
- Interfaces with **external DRAM** via AXI4 or native memory controller
- Typically implemented using FPGAs or specialized SoCs

---

## 🖧 Ethernet Fabric

- OmniXtend requires only standard Ethernet switches with MAC-level forwarding
- No IP configuration is needed
- Topologies supported:
  - Point-to-point links
  - Multi-drop (with broadcast)
  - Tree or mesh configurations

---

## 📍 Reference Design

### Example: 1 Master, 1 Memory Node

```
+------------------+         +------------------+
| RISC-V SoC       |  Eth    | FPGA-based       |
| + TileLink Master| <-----> | TileLink Slave   |
+------------------+         +------------------+
         |                            |
     Ethernet                    DRAM Controller
```

---

## ⚙️ Extensibility

- Supports multi-master systems with arbitration
- Extensible for different memory types (HBM, CXL, DDR)
- Future work includes:
  - Packet reliability
  - Security enhancements (e.g., MACsec)
  - Multicast/broadcast support for coherency

---

For more details on FPGA integration and implementation, see [Hardware Setup](hardware-setup.md).