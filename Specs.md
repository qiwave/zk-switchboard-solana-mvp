# Solana + Switchboard ZK Verification Pipeline — Full Specifications

This specification defines the architecture, state accounts, instructions, oracle integration, and execution flow for the private-proof verification system using Switchboard as the off-chain verifier and Solana as the on-chain state authority.

---

# 1. Architectural Overview

The system consists of:

* **User → Proof Generator**
  Generates ZK proofs locally.

* **Switchboard (Cerebellum Layer)**
  Off-chain proof verification. Emits `{ valid, proof_hash, nullifier }`.

* **Solana Program (Cortex Layer)**
  Validates oracle signature, checks validity, prevents double-use via nullifiers.

* **State Accounts (Long-Term Memory)**
  Store proof history, verification status, counters, and used nullifiers.

---

# 2. State Accounts

## 2.1 GlobalState

Tracks global system metrics and configuration.

```
pub struct GlobalState {
    pub is_initialized: bool,
    pub total_verified: u64,
    pub last_proof_hash: [u8; 32],
    pub authority: Pubkey,
}
```

## 2.2 UserState

Tracks per-user verification history.

```
pub struct UserState {
    pub user: Pubkey,
    pub is_verified: bool,
    pub last_proof_hash: [u8; 32],
    pub last_nullifier: [u8; 32],
}
```

## 2.3 NullifierState

Prevents reuse of the same proof/nullifier.

```
pub struct NullifierState {
    pub nullifier: [u8; 32],
    pub used: bool,
}
```

---

# 3. Oracle Attestation Format

This is the core object written by Switchboard into its aggregator account.

```
{
  "valid": true,
  "proof_hash": "0x…",
  "nullifier": "0x…",
  "verification_time_ms": 11,
  "timestamp": 1732160000,
  "user_pubkey": "<Pubkey>"
}
```

---

# 4. Instructions

## 4.1 InitializeGlobal

Initializes GlobalState.

```
Accounts:
- [signer] authority
- [writable] global_state
```

## 4.2 InitUserState

Initializes per-user state.

```
Accounts:
- [signer] user
- [writable] user_state
```

## 4.3 ProcessProof (Core Instruction)

Consumes Switchboard output and updates state.

Workflow:

1. Read oracle account.
2. Verify Switchboard signature.
3. Ensure `valid == true`.
4. Ensure nullifier unused.
5. Update UserState and GlobalState.
6. Create NullifierState.

Accounts:

```
- [signer] user
- [writable] user_state
- [writable] global_state
- [writable] nullifier_state
- [] switchboard_oracle_account
```

---

# 5. End-to-End Flow

1. User generates ZK proof.
2. User sends proof → Switchboard function.
3. Switchboard verifies proof.
4. Switchboard writes attestation to aggregator.
5. User signs a tx calling `process_proof`.
6. Program checks validity and updates state.
7. Proof hash + nullifier stored permanently.

---

# 6. Error Conditions

* Invalid oracle signature → reject.
* `valid == false` → reject.
* Nullifier already used → reject.
* UserState missing → reject.
* GlobalState uninitialized → reject.

---

# 7. Security Considerations

* All heavy ZK verification performed off-chain.
* Solana only trusts signed Switchboard output.
* Nullifiers prevent double-spends.
* Commitment hashes ensure privacy without revealing secrets.

---

# 8. Future Extensions

* Merkle tree membership proofs.
* Multiple proof types (Tier 1 / Tier 2 privacy levels).
* Batched verification.
* Rollup compatibility.
* Identity + attestations submodule.

---

# 9. Developer Notes

* PLONK recommended for early MVP.
* Proof size <3 KB tested.
* Expected oracle → Solana end-to-end latency: 150–300 ms.

---

This specification is sufficient for devs to implement the MVP pipeline and begin benchmarking immediately.
