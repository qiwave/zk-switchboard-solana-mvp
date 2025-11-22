# ZK → Switchboard → Solana Pipeline (MVP)

This repository contains the **open-source specification and benchmark plan** for a minimal zero-knowledge proof verification pipeline using **Switchboard** for off-chain proof verification and **Solana** for on-chain state validation.

The goal is to build and measure a small, elegant, and verifiable end-to-end system:

**ZK Proof → Switchboard verifies → Solana validates → State updated.**

This MVP is intentionally tiny so developers can experiment, benchmark, audit, and extend it.

---

## 🚀 Project Overview

This pipeline models a simple, privacy-preserving verification system where:

1. A user generates a ZK proof locally.
2. Switchboard verifies the proof off-chain.
3. Switchboard posts a signed attestation on-chain.
4. The Solana program:

   * validates the oracle result
   * prevents proof reuse via nullifiers
   * updates `UserState`, `GlobalState`, and `NullifierState`
5. All heavy ZK work happens off-chain; Solana stays lightweight.

This is designed for:

* Privacy applications
* Identity verification
* Proof-of-action systems
* zk-enabled DePIN models
* Grassroots and decentralized protocols

---

## 📦 Repository Contents

### **Specs.md**

A full architectural specification including:

* State accounts (Global, User, Nullifier)
* Instruction design
* Oracle attestation structure
* Execution flow
* Security notes

### **Benchmarks.md**

A complete benchmarking strategy covering:

* Off-chain verification time (ms)
* Solana compute units (CU)
* Data payload sizes (bytes)
* End-to-end latency
* A template results table

These benchmarks help evaluate real-world performance across different circuits and configurations.

---

## 🧪 MVP Benchmark Scenario

The initial test uses a small ZK circuit (any simple Circom/PLONK circuit works). The focus is not complexity—it’s measuring:

* Verification latency on Switchboard
* Payload compression
* Solana state-update cost
* Round-trip timing

Once the pipeline is running, more realistic privacy circuits can be plugged in.

---

## 🔧 Developer Goals

If you want to contribute, here are great starting tasks:

* Implement the Switchboard function that verifies a sample proof
* Build the Solana `process_proof` instruction
* Create the PDA layouts (GlobalState, UserState, NullifierState)
* Record benchmark metrics and append them to Benchmarks.md
* Propose circuit optimizations or variations
* Improve documentation or implementation notes

All contributions welcome.

---

## 🧠 Architectural Analogy (for intuition)

* **Switchboard = cerebellum**

  * Fast verification
  * Error correction
  * Produces compact signals

* **Solana Program = cortex**

  * Final decision-making
  * State transition logic

* **State Accounts = long-term memory**

  * Permanent proof and nullifier storage

This helps conceptualize workload distribution and latency trade-offs.

---

## 🤝 Contributing

Pull requests, discussions, and experiments are encouraged.
This MVP is built to be expanded, modified, and remixed.

If you're interested in building or testing the pipeline, open an issue or drop a PR.

---

## 📜 License

MIT License. Use freely.

---

## 🌐 Contact & Community

Contributors and curious builders are welcome.

If you create a fork or build a variation, tag it and share your results.

Let’s see how far we can push ZK + Switchboard + Solana together.
