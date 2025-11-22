# Benchmarks — ZK → Switchboard → Solana (Cerebellum Pipeline)

This document outlines benchmark procedures and metrics for the private-proof pipeline involving ZK proof verification off-chain via Switchboard, with on-chain validation and state updates in Solana.

## 1. Scenario Under Test (MVP #1)

**Flow:**

1. User generates a ZK proof.
2. Switchboard verifies proof off-chain.
3. Validity attestation is posted on-chain.
4. Solana program validates the attestation and updates state.

**State updates include:**

* `GlobalState.total_verified`
* `GlobalState.last_proof_hash`
* `UserState.is_verified`
* `UserState.last_proof_hash`
* `UserState.last_nullifier`
* Creation of `NullifierState`

## 2. Metrics to Measure

1. **Verification time (off-chain) — ms**
2. **Solana compute units (CU)**
3. **Data payload size (bytes)**
4. **End-to-end latency (ms)**

## 3. Instrumentation Details

### Off-Chain Verification Time

Measure time spent inside `verify_proof()`.

### Solana Compute Units

Extract CU usage from transaction logs during state update instructions.

### Data Payload Size

Measure input payload bytes sent to Switchboard and output attestation bytes written on-chain.

### End-to-End Latency

`t_solana_confirmed - t_switchboard_start`.

## 4. Benchmark Table Template

| Scenario | Proof system | Proof size (bytes) | verification_time_ms | oracle_output_bytes | solana_cu_used | end_to_end_ms |
| -------- | ------------ | ------------------ | -------------------- | ------------------- | -------------- | ------------- |
| MVP v1   | PLONK demo   | 2100               | 11                   | 180                 | 18,000         | 230           |

## 5. Implementation Order

1. Benchmark simple circuit.
2. Swap in realistic privacy circuit.
3. Compare metrics.

## 6. Cerebellum Analogy

Switchboard = cerebellum (error correction + verification)
Solana = cortex (final decision + state write)
State accounts = long-term memory.
