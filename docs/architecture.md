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

OmniXtend is built as a layered, modular stack to facilitate memory disaggregation and cache-coherent communication across compute and memory nodes via Ethernet.

```
+----------------------------+
| Application / Software    |
+----------------------------+
| TileLink (Coherence Layer)|
+----------------------------+
| Packetizer / Depacketizer |
+----------------------------+
| UDP over Ethernet         |
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

TileLink messages are encapsulated into OmniXtend packets structured as:

```
+-------------------+
| Ethernet Header   |
+-------------------+
| UDP Header        |
+-------------------+
| OmniXtend Header  |
+-------------------+
| TileLink Payload  |
+-------------------+
```

- **OmniXtend Header** includes:
  - Source/Destination node IDs
  - Channel type (A, B, C, D, E)
  - Transaction ID
  - Length

---

## 🧠 Memory Node (Responder)

- Receives TileLink packets over Ethernet
- Decodes and services memory requests
- Interfaces with **external DRAM** via AXI4 or native memory controller
- Can be implemented using FPGAs or SoCs

---

## 🖧 Ethernet Fabric

- Any standard Ethernet switch can be used (no need for custom interconnects)
- Supports:
  - Point-to-point links
  - Multi-drop (with broadcast)
  - Tree or mesh topologies

---

## 📍 Reference Design

### Example: 1 Master, 1 Memory Node

```
+------------------+         +------------------+
| RISC-V SoC       |  UDP    | FPGA-based       |
| + TileLink Master| <-----> | TileLink Slave   |
+------------------+         +------------------+
         |                            |
     Ethernet                    DRAM Controller
```

---

## ⚙️ Extensibility

- Supports multi-master systems with arbitration
- Extensible for different memory types (HBM, CXL, DDR)
- Future work: reliability, security (MAC/IPsec), multicast

---

For more details on FPGA integration and implementation, see [Hardware Setup](docs/hardware-setup.md).
