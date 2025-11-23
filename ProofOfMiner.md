# Proof of Miner on Solana for YEC

This document describes a simple application that uses the ZK → Switchboard → Solana pipeline to prove that a user is a real Ycash miner or holder, without revealing their addresses or balances.

The goal is to give Solana apps a way to recognise and reward YEC miners while keeping YEC privacy and consensus rules untouched.

## High level idea

1. YEC miner proves they control at least a threshold amount of YEC using a ZK circuit.
2. Switchboard verifies the ZK proof off chain and produces an attestation.
3. A Solana program verifies the Switchboard attestation and writes a PDA marking that wallet as a YEC miner. It may also mint a non transferable badge NFT.
4. Any Solana app can read that PDA or NFT and treat the wallet as a verified miner.

No changes are required to the Ycash protocol.

## ZK circuit

Private inputs  
• YEC spending or viewing key  
• Set of YEC UTXOs or shielded notes owned by the user

Public inputs  
• Threshold amount X  
• Epoch or time window identifier  
• Solana wallet public key S

The circuit proves  
“I control YEC notes whose total value is at least X, and I bind this proof to Solana wallet S for epoch E”

The circuit outputs a public hash H that commits to  
S, X, E and the internal witness.

## Switchboard job

Inputs  
• ZK proof  
• Public inputs (X, E, S)  
• Public hash H

Each Switchboard node  
1. Verifies the ZK proof against the verifying key.  
2. Recomputes H and checks that it matches the proof outputs.  
3. Signs an attestation containing H, S and E.

When a quorum of nodes agree, Switchboard produces an aggregate attestation for H, S and E.

## Solana program

The Solana program receives  
• The Switchboard attestation  
• The Solana wallet S  
• The PDA account that will store miner state

The program  
1. Verifies the Switchboard signatures and quorum.  
2. Checks that the epoch E is current and not expired.  
3. Derives the PDA from S and a fixed seed and verifies that the PDA matches.  
4. Writes or updates the PDA to mark S as a verified miner for epoch E.  
5. Optionally mints a non transferable “YEC Miner Badge” NFT to S.

The PDA can hold fields such as  
• wallet public key  
• latest verified epoch  
• optional weight or score

## How Solana apps can use it

Any Solana program can read this PDA or the badge NFT to  
• gate access to communities or chat  
• weight governance votes toward real miners  
• route airdrops or incentives to miners  
• build mining based reputation systems

## Why this matters

This design lets a proof of work privacy chain like Ycash project its economic reality into the Solana ecosystem without revealing addresses or breaking consensus.

YEC remains the source of value and security.  
Switchboard acts as the cerebellum that verifies the ZK proof.  
Solana acts as the cortex that stores the final engram in a PDA.

The result is a clean way to build a YEC miner movement on Solana using open source rails.
