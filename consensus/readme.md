# Consensus Package

## Overview

The consensus package implements the Tendermint consensus protocol tailored for the Juno blockchain. It manages the process of proposing, voting, and committing blocks in a Byzantine fault-tolerant manner. The package integrates with the P2P network to exchange consensus messages and maintains persistent state to recover from failures.

## Table of Contents

- [Architecture](#architecture)
- [Core Components](#core-components)
- [Key Features](#key-features)
- [Usage](#usage)
- [API Reference](#api-reference)
- [Examples](#examples)
- [Contributing](#contributing)
- [License](#license)

## Architecture

The consensus package is designed around a modular architecture that separates concerns such as consensus logic, networking, block proposing, and state persistence. It uses a state machine to process consensus messages and timeouts, driving the consensus rounds and steps. The package interacts with the blockchain layer to commit finalized blocks and uses a write-ahead log (WAL) database to ensure state recovery after crashes.

The P2P networking layer handles message broadcasting and listening, supporting efficient communication between validators. Proposals are streamed and validated concurrently, enabling scalable consensus operations.

### Component Overview

- **Tendermint Engine**: Implements the core Tendermint consensus state machine and rules.
- **Consensus Driver**: Coordinates message handling, state machine processing, and action execution.
- **Block Proposer**: Builds and manages block proposals, including transaction handling.
- **P2P Networking**: Manages message broadcasting and listening over libp2p pubsub topics.
- **Database Layer**: Provides persistent storage for consensus messages and timeouts via a WAL.
- **Types**: Defines core consensus message types, actions, and state representations.

## Core Components

### Tendermint Engine (consensus/tendermint)

The Tendermint engine implements the consensus state machine, processing proposals, prevotes, precommits, and timeouts according to Tendermint rules. It manages consensus rounds and steps, validates messages, and triggers actions such as broadcasting votes or committing blocks.

Key files:
- tendermint.go - Main Tendermint implementation
- process.go - Consensus process logic
- timeout.go - Timeout handling

### Consensus Driver (consensus/driver)

The driver listens for incoming consensus messages from the network and passes them to the Tendermint state machine. It executes actions returned by the state machine, such as broadcasting messages or scheduling timeouts. It also handles committing blocks and cleaning up WAL entries.

Key files:
- driver.go - Main driver implementation

### Block Proposer (consensus/proposer)

The proposer builds new block proposals by collecting transactions and interacting with the builder component. It manages the lifecycle of the preconfirmed block, re-runs transactions if needed, and submits proposals to the consensus engine.

Key files:
- proposer.go - Proposer logic

### P2P Networking (consensus/p2p)

The P2P package manages consensus message broadcasting and listening using libp2p pubsub. It includes components for proposal broadcasting, vote broadcasting, and proposal stream demultiplexing. It ensures messages are efficiently propagated and received among validators.

Key components:
- p2p.go - Main P2P implementation
- broadcaster.go - Message broadcasting
- listener.go - Message listening

### Database Layer (consensus/db)

The database layer provides persistent storage for consensus messages and timeouts using a write-ahead log (WAL). It batches writes to reduce disk I/O and supports replaying WAL entries to recover consensus state after restarts.

Key files:
- db.go - Database operations
- buckets_consensus.go - Storage buckets

### Types (consensus/types)

This package defines the core types used in consensus, including messages, proposals, votes, timeouts, and actions. It provides generic interfaces and concrete types specialized for Starknet integration.

Key types:
- messages.go - Message types
- proposal.go - Proposal types
- state.go - State types

## Key Features

1. **Byzantine Fault Tolerance**
   - Implements Tendermint consensus to tolerate up to one-third faulty or malicious validators.

2. **Block Proposal and Voting**
   - Supports proposing blocks, prevoting, and precommitting with strict protocol rules.

3. **State Persistence**
   - Uses a write-ahead log to persist consensus messages and timeouts for crash recovery.

4. **P2P Communication**
   - Integrates with libp2p pubsub for efficient message broadcasting and listening among validators.

## Usage

### Basic Integration

```go
// Example code showing basic usage
import "github.com/NethermindEth/juno/consensus"

// Initialize consensus
consensus := consensus.New(config)

// Start consensus
consensus.Start()
```

### Configuration

```go
// Example configuration
type Config struct {
